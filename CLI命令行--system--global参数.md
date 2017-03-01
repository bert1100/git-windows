## --system

系统级参数，需要拥有管理员权限，参数的含义将对所有用户起到作用。

例如：
$ sudo git config --system alias .st status
该命令：将注册的命令别名能够被所有用户所使用。
> 见《git权威指南》第二章

## --global
用户级全局参数，只在本用户的全局配置中起作用。
> 见《git权威指南》第二章

## 无参数或者--development(或者--dev)
项目级参数，作用于某一个具体的项目

--

## 系统级密钥位置（--system）

ssh密钥对可以存在于 windows的系统设置中，是属于系统级别的，以win 10为例具体目录是：
`C:\Windows\System32\config\systemprofile\.ssh`

### 用户级/全局级的ssh密钥对 `大家都在用-推荐`(--global）
ssh密钥对`通常`存在于用户的目录中，以win 10为例具体目录是：
`C:\Users\bert\.ssh`
有时被称为全局级的原因，我猜想是：当使用git命令：`git config --global user.name bert `时，这个命令其实写在当前用户的配置中。

### 项目级/仓库级的ssh密钥对
ssh密钥对也可以存在于某一个项目目录中，以win 10中有例为证：
android开发包中`C:\Users\bert\.android`存在密钥，不过一般存在的都是后缀.pub的公钥。
注意： 不一定是`.ssh`目录了，大多是项目的设置目录。





