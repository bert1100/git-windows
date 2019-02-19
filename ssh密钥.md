## 大话密钥对（以ssh为例）
### SSH加密解密原理

1. 客户端把自己的公钥放在要链接的远程主机上；
2. 客户端要链接远程主机的时候，远程主机会向客户端发送一条随机的字符串，客户端收到这个字符串之后，使用自己的私钥对字符串加密然后发送回给远程主机，远程主机根据自己存放的公钥对这个字符串进行解密，如果解密成功证明客户端是可信的，直接允许登录，不再要求其他登录方式。



### 系统级的ssh密钥对
ssh密钥对可以存在于 windows的系统设置中，是属于系统级别的，以win 10为例具体目录是（随着系统更新可能有变化）：
`C:\Windows\System32\config\systemprofile\.ssh`

### 用户级/全局级的ssh密钥对 `大家都在用-推荐`
ssh密钥对`通常`存在于用户的目录中，以win 10为例具体目录是：
`C:\Users\bert\.ssh`
有时被称为全局级的原因，我猜想是：当使用git命令：`git config --global user.name bert `时，这个命令其实写在当前用户的配置中。

### 项目级/仓库级的ssh密钥对
ssh密钥对也可以存在于某一个项目目录中，以win 10中有例为证：
android开发包中`C:\Users\bert\.android`存在密钥，不过一般存在的都是后缀.pub的公钥。
注意： 不一定是`.ssh`目录了，大多是项目的设置目录。


总结，一共三种位置，其他git的设置情况请举一反三



## 密钥的权限

非对称密钥对有私钥和公钥组成。主要注意的是私钥文件的权限是600，意为私钥不允许除拥有者外进行访问。

私钥：权限一般为600

> 私钥容易暴露，因此经常会被私钥再额外加上密码(Passphrase)

公钥：权限不限，它将放在被连接的远程主机上

**特别注意：**在.ssh/ 下一般放密钥对。

> 原因：一些软件会在进行ssh连接之前，对私钥和公钥进行校验。



## SSH的使用命令

- #### ssh-add

  `$ ssh-add -K [path/to/your/ssh-private-key]`

  这个命令不是用来永久性的记住你所使用的私钥的。实际上，它的作用只是把你的私钥添加到ssh-agent所管理的一个session当中。而ssh-agent是一个**用于存储私钥的临时性的session服务**，也就是说当你重启之后，ssh-agent服务也就重置了。

  在Mac os中，使用这个命令将ssh私钥添加到密钥串（keychain）服务当中，这样做的好处：对于有密码保护的私钥以后就可以不用再输入密码了。

  > 参考文章： https://segmentfault.com/q/1010000000835302/a-1020000000883441
  >
  > ssh-add命令是把专用密钥添加到ssh-agent的高速缓存中。该命令位置在/usr/bin/ssh-add。
  >
  > 语法： 
  > ssh-add [-cDdLlXx] [-t life] [file …] 
  > ssh-add -s pkcs11
  >
  > 选项 
  > -D：删除ssh-agent中的所有密钥. 
  > -d：从ssh-agent中的删除密钥 
  > -e pkcs11：删除PKCS#11共享库pkcs1提供的钥匙。 
  > -s pkcs11：添加PKCS#11共享库pkcs1提供的钥匙。 
  > -L：显示ssh-agent中的公钥 
  > -l：显示ssh-agent中的密钥 
  > -t life：对加载的密钥设置超时时间，超时ssh-agent将自动卸载密钥 
  > -X：对ssh-agent进行解锁 
  > -x：对ssh-agent进行加锁 
  > 实例： 
  > 1、把专用密钥添加到 ssh-agent 的高速缓存中： 
  > ssh-add ~/.ssh/id_dsa
  >
  > 2、从ssh-agent中删除密钥： 
  > ssh-add -d ~/.ssh/id_xxx.pub
  >
  > 3、查看ssh-agent中的密钥： 
  >
  > ssh-add -l 

## 我的ssh密钥

- 位置：C:\Users\bert (对应的文件夹是：本地磁盘C>用户>bert)
- 文件：.ssh/目录 就是密钥所在的文件夹（一般是隐藏的）


## 如何生成ssh密钥
请参考github在设置中会有相应的文档说明，照着做即可。







### 

