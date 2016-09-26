# git的使用

## 配置SSH keys链接github
1 检查本机是否有ssh key设置  `$ cd ~/.ssh` 或`cd .ssh`
如果没有则提示： `No such file or directory`
如果有则进入`~/.ssh`路径下（`ls`查看当前路径文件，`rm *` 删除所有文件）

2 使用Git Bash生成新的ssh key。
$ cd ~  #保证当前路径在”~”下
$ ssh-keygen -t rsa -C "xxxxxx@yy.com"  #建议填写自己真实有效的邮箱地址
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/xxxx_000/.ssh/id_rsa):   #不填直接回车
Enter passphrase (empty for no passphrase):   #输入密码（可以为空）
Enter same passphrase again:   #再次确认密码（可以为空）
Your identification has been saved in /c/Users/xxxx_000/.ssh/id_rsa.   #生成的密钥
Your public key has been saved in /c/Users/xxxx_000/.ssh/id_rsa.pub.  #生成的公钥
The key fingerprint is:
e3:51:33:xx:xx:xx:xx:xxx:61:28:83:e2:81 xxxxxx@yy.com
*本机已完成ssh key设置，其存放路径为：c:/Users/xxxx_000/.ssh/下。
注释：可生成ssh key自定义名称的密钥，默认id_rsa。
$ ssh-keygen -t rsa -C "邮箱地址" -f ~/.ssh/githug_blog_keys #生成ssh key的名称为githug_blog_keys，慎用容易出现其它异常。

3 添加ssh key到GItHub

3.1登录GitHub系统；点击右上角账号头像的“▼”→Settings→SSH kyes→Add SSH key。

3.2 复制id_rsa.pub的公钥内容。 
1) 进入c:/Users/xxxx_000/.ssh/目录下，打开id_rsa.pub文件，全选复制公钥内容。
2) Title自定义，将公钥粘贴到GitHub中Add an SSH key的key输入框，最后“Add Key”。

4 配置账户
$ git config --global user.name “your_username”  #设置用户名
$ git config --global user.email “your_registered_github_Email”  #设置邮箱地址(建议用注册giuhub的邮箱)

5 测试ssh keys是否设置成功。
$ ssh -T git@github.com
The authenticity of host 'github.com (192.30.252.129)' can't be established.
RSA key fingerprint is 16:27:xx:xx:xx:xx:xx:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)? yes #确认你是否继续联系，输入yes
Warning: Permanently added 'github.com,192.30.252.129' (RSA) to the list of known hosts.
Enter passphrase for key '/c/Users/xxxx_000/.ssh/id_rsa':  #生成ssh kye是密码为空则无此项，若设置有密码则有此项且，输入生成ssh key时设置的密码即可。
Hi xxx! You've successfully authenticated, but GitHub does not provide shell access. #出现词句话，说明设置成功。

## 将本地项目通过SSH push到GitHub
1 在github上创建一个仓库: “+▼”→New repository
2 复制test ssh key的`ssh`路径(注意不是https路径)
3 创建本地项目（以test为例）
  1) 创建目录
  $ mkdir test
  $ cd test
  2) 初始化
  $ git init
  3) 创建hello.md文件
  $ echo "这是一次测试test ssh key" > hello.md
  4) 提交到本地
  若出现如上warning提示则重新提交一次即可。
  $ git add .   #提交当前目录下所以文件
  $ git commit -m "add hello.md"   #提交记录说明 
  5) 提交到github
  $ git remote add origin ‘粘贴复制test ssh key的ssh路径’  #
  $ git push -u origin master
  Enter passphrase for key '/c/Users/hgpin_000/.ssh/id_rsa':  #ssh key设置密码故此需要输入密码

4 刷新test ssh key仓库，查看是否生效。

（完）

### git设置源仓库用法
git remote set-url origin URL

git remote set-branches [--add] <name> <branch>...
git remote set-url [--push] <name> <newurl> [<oldurl>]
git remote set-url --add <name> <newurl>
git remote set-url --delete <name> <url>

git remote rm origin
git remote add origin git@github.com:Liutos/foobar.git

## 使用git常见问题集合

### windows使用git时出现：warning: LF will be replaced by CRLF ？

`windows`中的换行符为 `CRLF`， 而在linux下的换行符为`LF`，所以在执行`add .` 时出现提示.

**解决办法：**
`$ rm -rf .git  // 删除.git ` 
`$ git config --global core.autocrlf false  //禁用自动转换`
然后重新执行：
`$ git init` 
`$ git add .` 

**原因分析：**
`CRLF` -- `Carriage-Return Line-Feed` 回车换行
就是回车(`CR`, `ASCII 13`, `\r`) 换行(`LF`, `ASCII 10`, `\n`)。
这两个`ACSII`字符不会在屏幕有任何输出，但在`Windows`中广泛使用来标识一行的结束。而在`Linux/UNIX`系统中只有换行符。
也就是说在`windows`中的换行符为 `CRLF`， 而在linux下的换行符为：`LF`
使用git来生成一个项目工程后，文件中的换行符为`LF`， 当执行`git add .`时，系统提示：`LF` 将被转换成 `CRLF`


### 如何修改git项目中 remote.origin.url？

做法1（较为谨慎，不影响其他origin的url）：
`git remote rename origin old_origin`
`git remote add origin url://new/url.git`

做法2（直接全部重置，不在乎其他origin的url）：
`git config remote.origin.url url://new/url.git`

做法3（使用命令修改，但git版本必须 (>1.7.0)）：
`git remote set-url origin url://new/url.git`


## git 参考文档如下：

- [window下配置SSH连接GitHub和GitHub配置ssh key](http://jingyan.baidu.com/album/a65957f4e91ccf24e77f9b11.html)
- [Git使用笔记 - 飞鸿影](http://www.tuicool.com/articles/mEvaq2)
- [遇到问题：fatal: refusing to merge unrelated histories ](http://blog.csdn.net/u010853261/article/details/51935503 

