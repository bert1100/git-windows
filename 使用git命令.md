#  常用git命令的实践

## 创建git仓库

> ```
> $ git init
>
> Initialized empty Git repository in /Users/52fhy/fhyblog/.git/
> ```
>
> 瞬间Git就把仓库建好了，而且告诉你是一个空的仓库（empty Git repository），细心的读者可以发现当前目录下多了一个 .git 的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。



## 查看状态（可随时执行）

```
$ git status
```

> 查看当前目录下git的状态：哪些文件被修改了、删除了、新增了。



## 提交到暂存区stage/index

```
$ git add .
```

> . 指当前所有目录及文件

```
$ git add readme.md
```

> 上述命令仅提交一个文件 readme.md

```
$ git add src/
```

> 上述命令则是提交一个目录src/

```
$ git reset HEAD readme.md
```

> 上述命令是撤销暂存区的改变，即恢复暂存区到最近的上一次历史。

```
$ git checkout -- readme.md
```

> 最后，上述命令是清理`工作区` 到上个版本最新状态。
> `git checkout -- file`命令中的`--`很重要，没有`--`，就变成了“切换到另一个分支”的命令。

## 提交到本地版本库HEAD

```
$ git commit -m "相关说明"
```

> 提交到本地版本库，使git可以跟踪到版本



## 如何撤销修改

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[版本回退](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013744142037508cf42e51debf49668810645e02887691000)一节，不过前提是没有推送到远程库。



## 版本回退

```
$ git log
```

> 先查看提交日志

```
$ git reset --hard HEAD^
```

> 回退到上一版本，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

```
$ git reset --hard 1094a
```

> 还可以找到以前的版本，再穿梭回来。版本号没必要写全，前几位就可以了，Git会自动去找。

```
$ git checkout --
```

> 然后更新工作区的文件

```
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

> 后悔药：查看以前的commit id，方便回退。



## 提交到远程版本库

```
  $ git push origin master
```

**前提：** 必须设置过一个远程版本库（详细参考： **使用前的git配置.md** ）

  ```
  $ git remote add origin ‘粘贴复制test ssh key的ssh路径’  #
  $ git push -u origin master
  Enter passphrase for key '/c/Users/hgpin_000/.ssh/id_rsa':  #ssh key设置密码故此需要输入密码
  ```



## 从远程版本库克隆到本地

```
 $ git clone git@github.com:bert1100/sublime-config.git  
```

> 上述命令使用了ssh方式进行克隆

```
 $ git clone https://github.com/bert1100/sublime-config.git
```

> 上述命令使用了https方式进行克隆



## 从远程版本库更新

```
 $ git pull origin master
```



## 恢复刚删除掉的文件（此时：还未加入暂存区）

```shell
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    git的使用.md
        modified:   ssh运用.md

```

```bash
$ git checkout -- .
```

> 上述命令将恢复当前目录中所有被删除的文件，例如，已经被标示为`deleted`的 `git的使用.md`



## 一气呵成：提交所有并推送到远程

```
$ git add . && git commit -m "change all files" && git push origin master
```

> && 符号是linux的命令特征，它保证前面的命令必须执行。



### 如何修改git项目中 remote.origin.url？

**做法1**（较为谨慎，不影响其他origin的url）：
```
git remote rename origin old_origin
git remote add origin url://new/url.git
```

**做法2**（直接全部重置，不在乎其他origin的url）：
```
git config remote.origin.url url://new/url.git
```
**做法3**（使用命令修改，但git版本必须 (>1.7.0)）：
```
git remote set-url origin url://new/url.git
```

### 先建项目后添加远程git仓库步骤

step1. 创建文件夹，然后在里面放一些文件
```
mkdir ldapclient
... //创建一些其他文件
```

step2. 加入git 追踪
```
git init
... //然后可以在本地做一些版本提交等操作
```
step3. 添加 git 远程仓库源
```
git remote add origin git@github.com:bert1100/ldapclient.git
git remote -v    //查看源
```

step4. 拉取 git 远程仓库源origin合并到本地源master
```
git pull //错误！此时会报错，因为git不知道你要合并哪个分支
git pull origin master //正确！需要指明，git才能关联起来，git是不是傻？
```

step5. 提交到 git 远程仓库源
```
git push //错误！此时仍然会报错，因为git不知道你要合并哪个分支
git push origin master //正确！
```
> git push 的提示：
fatal: 当前分支 master 没有对应的上游分支。
为推送当前分支并建立与远程上游的跟踪，使用

    `git push --set-upstream origin master`
    
## git 参考文档如下：

- [Git使用笔记 - 飞鸿影](http://www.tuicool.com/articles/mEvaq2)

  ​

