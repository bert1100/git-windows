## SSH的运用（以mac为例，其他系统类似）
在日常的工作和学习中，经常碰到需要配置不同的ssh-key来对应不同的环境。那么问题来了：如何同时在github、gitlab等项目上进行开发呢？

这就需要在系统下配置多个SSH-KEY。

### 预习： 生成不同的ssh-key(都必须会)
方法都一样，详细请看ssh密钥.md，一般情况下默认是以下命令：
`ssh-keygen -t rsa -C "youremail@yourcompany.com" -f ~/.ssh/id_rsa_github`

> 解释一下： 以上命令将在～/.ssh/目录下会生成私钥id_rsa_github 和 公钥id_rsa_github.pub。我们将公钥id_rsa_github.pub中的内容粘贴到github服务器上的ssh-key配置中，就可以免密登录了。



### 预备知识

ssh实际上是仍是一个软件，它有自己的文件配置。以mac为例：

ssh的系统级配置（也就是全局配置）位于 /etc/ssh/ssh_config 和 /etc/ssh/sshd_config （sshd_config 是作为目标主机需要配置的文件，需要确认打开：RSAAuthentication yes 、PubkeyAuthentication yes 和用于验证公钥key所放的位置 AuthorizedKeysFile .ssh/authorized_keys，最后还要重启远程主机的ssh服务以便生效：/etc/init.d/ssh restart）

```basic
#中文详解  文件位置：/etc/ssh/ssh_config 
Host *
选项“Host”只对能够匹配后面字串的计算机有效。“*”表示所有的计算机。

ServerAliveInterval 60
“ServerAliveInterval”:设置空闲连接超时时间，即 60s后ssh断开与服务器的连接。

ForwardAgent no
“ForwardAgent”:设置连接是否经过验证代理（如果存在）转发给远程计算机。

ForwardX11 no
“ForwardX11”设置X11连接是否被自动重定向到安全的通道和显示集（DISPLAY set）。

RhostsAuthentication no
“RhostsAuthentication”设置是否使用基于rhosts的安全验证。

RhostsRSAAuthentication no“RhostsRSAAuthentication”
设置是否使用用RSA的基于rhosts的安全验证。

RSAAuthentication yes“RSAAuthentication”
设置是否使用RSA算法进行安全验证。

PasswordAuthentication yes“PasswordAuthentication”
设置是否使用口令验证。

FallBackToRsh no“FallBackToRsh”
设置如果用ssh连接出现错误是否自动使用rsh。

UseRsh no“UseRsh”
设置是否在这台计算机上使用“rlogin/rsh”。

BatchMode no“BatchMode”
如果设为“yes”，

passphrase/password
（交互式输入口令）的提示将被禁止。当不能交互式输入口令的时候，这个选项对脚本文件和批处理任务十分有用。

CheckHostIP yes“CheckHostIP”设置ssh是否查看连接到服务器的主机的IP地址以防止DNS欺骗。建议设置为“yes”。

StrictHostKeyChecking no“StrictHostKeyChecking”
如果设置成“yes”，ssh就不会自动把计算机的密匙加入“$HOME/.ssh/known_hosts”文件，并且一旦计算机的密匙发生了变化，就拒绝连接。

IdentityFile ~/.ssh/identity“IdentityFile”
设置从哪个文件读取用户的RSA安全验证标识。

Port 22“Port”
设置连接到远程主机的端口。

Cipher blowfish“Cipher”
设置加密用的密码。

EscapeChar ~“EscapeChar”
设置escape字符。

作者：chuan_bai
链接：http://www.jianshu.com/p/31ea8c46487b
```

### 情景(适用于自动化部署)

假如你有三台服务器 s1.x.com s2.x.com s3.x.com，而且你都做好了登录到每一台机器的免密码功能。就是通过本地机器直接运行 `ssh username@sN.x.com` （注意：*N*）都可以连接到相应的服务器了，那么问题就来了，如果你现在已经连接到 s1 这台机器上，希望通过 s1 这台机器直接连到 s2 这台机器，并且不要输入密码，你测试了一下发现不行，为什么呢？因为 s1 和 s2 这两台机器没有做免密码登录功能，虽然我们可以把本地的私钥上传到 s1 服务器上，让它连接 s2 的时候就使用这个私钥，但是这样子做太危险了啊，如果有人进了你的系统，你整个内网都是可以直接进入的，因为已经拿到了你的私钥了嘛。`ssh-agent` 就是一个让你不把私钥上传到服务器上，就可以完成刚才的功能的一个工具，让你在这些服务器之间来去自如。

### step1. 开启ssh-agent

`$ eval ssh-agent`
`Agent pid XXX`
有时被称为全局级的原因，我猜想是：当使用git命令：`git config --global user.name bert `时，这个命令其实写在当前用户的配置中。

### step2. 添加私钥
`$ ssh-add ~/.ssh/id_rsa` 

> 如果生成密钥时是使用的默认的id_rsa文件，那么就是这个了，如果不是的话就写你的私钥地址吧。
> 如果报错：
>
>   ~ ssh-add ~/Documents/CRTkey/Identity              
>
> @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
>
> @         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
>
> @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
>
> Permissions 0644 for '/Users/weiwei/Documents/CRTkey/Identity' are too open.
>
> It is required that your private key files are NOT accessible by others.
>
> This private key will be ignored.
>
> 翻译一下：意思是说这个路径下的私钥权限被太多人看到了，私钥必须不能被其他人所访问，因此这个私钥添加被忽略。
>
> 解决办法： chmod 700 ~/Documents/CRTkey/Identity  把其他人权限统统去掉。

### step3. 告诉ssh 允许 ssh-agent 转发

- 修改全局(其实就是系统级)：

  > `$ echo "ForwardAgent yes" >> /etc/ssh/ssh_config`
  >
  > 注意：系统不同 ssh的config位置也不同

- 修改个人

  >$ touch ~/.ssh/config
  >$ vim ~/.ssh/config
  >Host *
  >　　ForwardAgent yes

- **其他知识**

  >  \# 将私钥加入mac的钥匙串
  >  ssh-add -K ~/yourpath/privateKey
  >  \#查看私钥列表
  >  ssh-add -l
  >
  >  \# 清空私钥列表
  >
  >  ssh-add -D

### step4. 在~/.ssh/config配置密钥对

> 提示：可使用 ssh -v usr@host 方式来测试一下，访问这个host用的哪个密钥。

```shell
# 添加config配置文件
# 配置文件参数
# Host: Host可以看作是一个你要识别的模式，对识别的模式，进行配置对应的主机名和ssh文件
# HostName： 你要登录的主机名
# User：登录名
# PubkeyAcceptedKeyTypes 默认是rsa，会报错，我这里使用的+ssh-dss，意思加上ssh-dss密钥方式（即DSA，由于DSA加密方式比较weak，因此在mac上的ssh默认下是不启用的）
# IdentityFile： 指明上面User对应的IdentityFile路径(特别注意：这里放的是私钥)

# github实例
Host Build software better,togethor（这里是github的域名或者ip）
   HostName  Build software better,togethor
   PreferredAuthentications publickey
   PubkeyAcceptedKeyTypes +ssh-dss 
   IdentityFile ~/.ssh/id_rsa_githhub
```



### step5. 修改每台服务器的 ssh 配置文件，让它们都对 ssh-agent 进行转发

到每台服务器上去按 step3 -> 全局，做一下。

现在你可以通过本地连接到任意一台服务器了，通过这台服务器再任意的连接到其它的服务器，以后就可以静静地享受这份快乐了！



------

## ssh问题汇总

- 清除原有ssh密钥方法

  当远程git-server的用户账号或者密钥发生了更改，需要清理下 `known_hosts`

  > ssh-keygen -R 192.168.1.203(你远程服务器的IP)



#### ssh登录提示
- 当远程主机的公钥被接受以后，它就会被保存在文件$HOME/.ssh/known_hosts之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。
- 每个SSH用户都有自己的known_hosts文件，此外系统也有一个这样的文件，通常是/etc/ssh/ssh_known_hosts，保存一些对所有用户都可信赖的远程主机的公钥。


#### ssh登录的其他知识
> 以下信息转自 阮一峰的 [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)

**公钥登录**

如果使用密码登录，每次都必须输入密码，非常麻烦。好在SSH还提供了公钥登录，可以省去输入密码的步骤。

所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

这种方法要求用户必须提供自己的公钥。如果没有现成的，可以直接用ssh-keygen生成一个：

> 　　$ ssh-keygen

运行上面的命令以后，系统会出现一系列提示，可以一路回车。其中有一个问题是，要不要对私钥设置口令（passphrase），如果担心私钥的安全，这里可以设置一个。

运行结束以后，在$HOME/.ssh/目录下，会新生成两个文件：id_rsa.pub和id_rsa。前者是你的公钥，后者是你的私钥。

这时再输入下面的命令，将公钥传送到远程主机host上面：

> 　　$ ssh-copy-id user@host

好了，从此你再登录，就不需要输入密码了。

如果还是不行，就打开远程主机的/etc/ssh/sshd_config这个文件，检查下面几行前面"#"注释是否取掉。

> 　　RSAAuthentication yes
> 　　PubkeyAuthentication yes
> 　　AuthorizedKeysFile .ssh/authorized_keys

然后，重启远程主机的ssh服务。

> 　　// ubuntu系统
> 　　service ssh restart
>
> 　　// debian系统
> 　　/etc/init.d/ssh restart

**六、authorized_keys文件**

远程主机将用户的公钥，保存在登录后的用户主目录的$HOME/.ssh/authorized_keys文件中。公钥就是一段字符串，只要把它追加在authorized_keys文件的末尾就行了。
这里不使用上面的ssh-copy-id命令，改用下面的命令，解释公钥的保存过程：

> 　　$ ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub

这条命令由多个语句组成，依次分解开来看：（1）"\$ ssh user@host"，表示登录远程主机；（2）单引号中的mkdir .ssh && cat >> .ssh/authorized_keys，表示登录后在远程shell上执行的命令：（3）"$ mkdir -p .ssh"的作用是，如果用户主目录中的.ssh目录不存在，就创建一个；（4）'cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub的作用是，将本地的公钥文件~/.ssh/id_rsa.pub，重 定向追加到远程文件authorized_keys的末尾。

写入authorized_keys文件后，公钥登录的设置就完成了。


