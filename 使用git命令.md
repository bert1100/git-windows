# 常用git命令的实践

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



## 加入到本地版本库（提交到暂存区）

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



## 提交到本地版本库

```
$ git commit -m "相关说明"
```

> 提交到本地版本库，使git可以跟踪到版本



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


## git 参考文档如下：

- [Git使用笔记 - 飞鸿影](http://www.tuicool.com/articles/mEvaq2)

  ​

