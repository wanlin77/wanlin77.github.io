---
title: 一台计算机配置多个git账户
date: 2023-10-05 18:49:59
categories:
  - [Coder, Git]
tags:
  - Git
  - Tutorial
---
<br>
&emsp;&emsp; 写作缘由：为了记住，在一台计算机上，配置多个git账户

## 配置步骤
- Step 1 : 为所有账户创建`SSH key`
- Step 2 : 将`SSH Key`添加至`SSH Agent`
- Step 3 : 将`SSH公钥`添加至github或其他gitlab等git托管网站
- Step 4 : 创建配置文件，完成多账户配置
- Step 5 : 使用不同账户从gitlab上clone

<br>
<hr>

### Step 1

#### 为所有账户创建`SSH key`

进入`.ssh`文件夹
```shell script
cd ~/.ssh #windows用户: C:\Users\your_account\.ssh
```

使用命令行，生成`ssh key`，按提示输入密码，可以不填密码一路回车
```shell script
ssh-keygen -t ed25519 -C "personal_email@gmail.com" -f ~/.ssh/id_rsa_github
ssh-keygen -t ed25519 -C "office_email@gmail.com" -f ~/.ssh/id_rsa_work
```

`ssh key`类型较多，一般推荐使用`ed25519`或`rsa`，
ed25519是目前最安全、加解密速度最快的key类型，由于其数学特性，它的key的长度比rsa小很多，优先推荐使用

<hr>
<br>

### Step 2

#### 将`SSH Key`添加至`SSH Agent`

将生成的SSH添加至SSH-Agent
```shell script
ssh-add -K ~/.ssh/id_rsa_github
ssh-add -K ~/.ssh/id_rsa_work
```

查看是否添加成功
```shell script
ssh-add -l
```

<hr>
<br>

### Step 3

#### 将`SSH公钥`添加至github或其他gitlab等git托管网站

**1.复制公钥**  
将`~/.ssh/`下的生成的公钥（生成的.pub文件），进行复制
```shell script
clip < ~/.ssh/id_rsa_github.pub
```

**2.在gitlab中，配置SSH**
- 登录gitlab网站（github或公司搭建的代码托管平台）
- 进入 Settings > SSH and GPG keys > New SSH Key
- 将复制的公钥粘贴进去，并设置一个title

<hr>
<br>

### Step 4

#### 创建配置文件，完成多账户配置

如果`~/.ssh`目录不存在config文件，创建配置文件
```shell script
touch config
```

配置的文件内容：
```
# 账号1-github
Host github.com
Hostname github.com
User one
PreferredAuthentications publickey
IdentityFile ~\.ssh\id_rsa_github

# 账号2-公司的gitlab
Host ctripcorp.com
Hostname company_git_lab_website # such as : git.xxx.yyy.com
User two
PreferredAuthentications publickey
IdentityFile ~\.ssh\id_rsa_work
```

<hr>
<br>

### Step 5

#### 使用不同账户从gitlab上clone

现在，就可以直接使用git，从不同代码托管平台，分别clone项目至本地  
```shell script
git clone git@github.com:{owner-user-name}/{the-repo-name}
```
