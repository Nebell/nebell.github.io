---
title: "使用 SSH 密钥以对 GitHub 进行认证"
date: 2021-11-01T21:58:25+08:00
tags: [ "Github", "SSH" ]
excerpt: "无论是出于 GitHub 的强制要求的应对，还是出于便利的考虑，使用 SSH 密钥验证不失为一个安全又便利的可选项。虽然我是过了八月之后 Push 发现不得了才使用 SSH KEY 的😂。本文使用 OpenSSH 配套工具以及 PuTTY 配套工具生成 SSH 密钥的同时给 GitHub 开启了认证！😀"
draft: true
---

### 前言

> For developers, if you are using a password to authenticate Git operations with GitHub.com today, you must begin using [a personal access token](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/creating-a-personal-access-token) over HTTPS (recommended) or SSH key by August 13, 2021, to avoid  disruption. If you receive a warning that you are using an outdated  third-party integration, you should update your client to the latest  version.

无论是出于 GitHub 的强制要求的应对，还是出于便利的考虑，使用 SSH 密钥验证不失为一个安全又便利的可选项。

虽然我是过了八月之后 Push 发现不得了才使用 SSH KEY 的😂。

### SSH KEY 生成

#### 使用 OpenSSH 配套工具

一般是在 Linux 下使用 `ssh-keygen` 生成密钥。

当然笔者是在 Windows 下使用这套工具的，如需使用请参考[【安装 OpenSSH】](https://docs.microsoft.com/zh-cn/windows-server/administration/openssh/openssh_install_firstuse#install-openssh-using-windows-settings)进行安装；

而 Linux 下命令大同小异🧐，使用相同命令敲即可（可能需要先安装 OpenSSH 套件）。

使用 `ssh-keygen` 生成 RSA 密钥并附带注释

`ssh-keygen -t rsa -C example@email.com`

输出一般长这样：

```powershell
PS C:\> ssh-keygen -t rsa -C example@email.com
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\User/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
SHA256:nSUAPH6/IivkL2k+c/b8vYsobUPu4qS+Eaij1aWNu8A example@email.com
The key's randomart image is:
+---[RSA 3072]----+
|     ....        |
|      o  .       |
|     . .  . .    |
|   .  . .. +     |
|  . . ..S.o      |
| o ..*  . .      |
|o Eo=.o+   .     |
|.o .O=*.*..o     |
|.  +B%+O=+o +o   |
+----[SHA256]-----+
```

查看并复制生成出来的密钥，默认生成的私钥🔑是 `id_rsa`，公钥🗝是同名且以 `.pub` 为拓展名的文件。

**默认生成的密钥地址**应该为 `C:\Users\用户名\.ssh\id_rsa`。

我这里是使用 `cat` 进行查看。`cat id_rsa` 或者使用编辑器打开新生成的公钥，公钥长这样：

```text
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDz+uBFXJ+r/dZ+APS0iQ7/GpWpgrrZhNPw/8YMRBTwC9S13/ibKEzVJiHGFqq3wSkgAIyYQ/Fi56uChJH9DB2rtIXDsTFzQNHHrhZnBD8BHxaMeX2WktMohZYqahn1Tkq40txjJrEc8pYwrm11amYaJoBuK17+BJXxB+j+Ehs2SA56rSBBL08UC38vW+SZm6xrd3vN25PzjXSiqasEf7O1nDeFa/vtVi0aJBj6dJfFFefi/eMdxcuIrRHwbfMyzJYFYDrRL7Z3zF+oBeitK3gYhMcF/P13ibCx1vgq6TnBbZHLLwfTkGKNl5g9dRaYj22yR6vIe1GGPSvP/A+K3/4ofLHRS9JcFBhuHbbU2sVjz691Kon2jdxjaCGIQX43O+1AfHFsLJGfvjXIDbEHKu9Eq8h3x75MqAdRSYpwPIV+1XqepATMf8YUH8AiyWN5QUVgvkCEXEhemp8RkWVaJ1ZuZjwTh9ab7Ahtfj3iW5B0AYwrPyZEKRfXSN8dYsYjAZM= example@email.com
```

那么将公钥复制，进行下一步。

#### 使用 PuTTY Key Generator

一般在 Windows 下使用这个 PuTTY 的配套工具生成密钥。

建议访问 [PuTTY 官网](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)进行下载，以防下载到**带恶意软件捆绑的版本**🤦‍♂️。

![image-20211102102808934](/img/git-ssh-key-verify/image-20211102102808934.png)

下载后打开，使用默认配置开始生成，点击【Generate】：

![image-20211102102917196](/img/git-ssh-key-verify/image-20211102102917196.png)

窗口会提示你在框框内**来回移动**鼠标🖱。

生成完成后，在菜单栏里选择【Conversions -> Export OpenSSH Key】保存 OpenSSH 格式的私钥🔑。

注意我们此处用的是 OpenSSH 格式的私钥，因为大多数 Linux 平台下使用 OpenSSH。

当然如果你想保存 PuTTY 格式的，选择下方的 【Save private key】进行保存就行👌。

保存为 `.ppk` 格式，可以用以 PuTTY 的认证或者 Windows 平台下 TortoiseGit 中 GitHub 的私钥认证。

![image-20211102103138682](/img/git-ssh-key-verify/image-20211102103138682.png)

保存或者将公钥部分复制下来备用。

但其实保存私钥更为重要，因为随时可以通过私钥生成🛠对应的公钥。

![image-20211102103448558](/img/git-ssh-key-verify/image-20211102103448558.png)

### GitHub 使用密钥进行认证

#### 设置 GitHub 的密钥认证

进入 GitHub 官网，从右上角的头像中进入设置⚙页面。

![gh-profile](/img/git-ssh-key-verify/gh-profile.png)

进入【SSH and GPG keys -> New SSH Key】。

![image-20211102110937387](/img/git-ssh-key-verify/image-20211102110937387.png)

进入后，将上文生成的公钥🗝粘贴进去，标题起一个你喜欢的名字（或者我喜欢的也行）。

![image-20211102111216900](/img/git-ssh-key-verify/image-20211102111216900.png)

添加完成后就会如下所示：

![image-20211102111316504](/img/git-ssh-key-verify/image-20211102111316504.png)

#### 本机设置 SSH 指定密钥认证

好的，终于开始了令人激动的认证👀环节！

以下假设对应粘贴到 Github 的公钥的私钥文件为 `id_rsa`。

#### 验证

一般而言，通过 OpenSSH 配套工具 `ssh-keygen` 生成的都可以直接使用了。

使用 `ssh -T git@github.com` 进行检验，返回以下信息说明成功！

```text
PS C:\> ssh -T git@github.com
Hi (你的用户名)! You've successfully authenticated, but GitHub does not provide shell access.
```

这个时候进行私有仓库的 Push / Clone / Pull 操作试试。

#### 添加私钥

如果是 PuTTY Key Generator 生成的私钥🔑，需要加入到 OpenSSH 中。

OpenSSH 添加私钥的操作，Linux / Windows 下都是：

`ssh-add (私钥文件地址)`

至于 Windows 下其他的客户端，请参考[这里](https://blog.csdn.net/u014180504/article/details/91851861)进行设置。

在 Windows 添加时，可能会出现 `ssh-agent` 服务未启动的问题，那么就将它启动。

```powershell
# 启动 ssh-agent 服务
PS C:\windows\system32> Start-Service ssh-agent
# 将 ssh-agent 设置为开机启动（可选）
PS C:\windows\system32> Set-Service -StartupType Automatic ssh-agent
# 查看服务状态
PS C:\windows\system32> Get-Service ssh-agent
```

或者参考[阿里云的文章](https://developer.aliyun.com/article/784983)通过图形化界面操作

> 右键“我的电脑”（Windows 10上为“此电脑”），选择“管理”菜单项，打开计算机管理窗口。
>
> 1. 左栏选择服务；
> 2. 右栏中找到OpenSSH Authentication Agent服务；
> 3. 双击打开设置界面，将启动类型由禁用改为自动；
> 4. 点击应用，这时才可以手动启动和停止agent服务；
> 5. 点击启动按钮，启动agent服务。

那么重试验证那一步，可以的话那就 OK 了。

#### 指定私钥认证

但是我又遇到了一个问题，出现在了我更换了私钥之后，甚至是使用 `ssh-add` 添加了私钥之后。

```text
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

**`ssh` 验证是成功的，而使用 `git` 拉取或者上传仓库则提示认证失败😅**

那么咱就**指定密钥文件**认证 GitHub，以下是 OpenSSH 的设置，依旧是平台无关（使用 Powershell 的话命令一样的）。

`cd ~/.ssh` 进入到 OpenSSH 的目录中。

创建并编辑 `config` 文件：

+ Linux 下可以 `vim config`
+ Windows 下可以 `notepad config`

格式大概长这样，**注意** Host 部分是要填写 `github.com` 的**而非随便起名的**，笔者有被无良教程坑害到：

```text
Host github.com
    Hostname github.com
    User git
    IdentityFile C:\path\to\privateKey
```

+ Host / Hostname => github.com
+ User => git 或者你的用户名经测试都可行
+ IdentityFile => 上文生成的 OpenSSH 的密钥地址

好了👌，再次进行第一步测试，不出意外就 OK 了。

### 其他

对于 Gitee / GitLab 的密钥设置是大同小异的，而使用 SSH 密钥进行 SSH 连接可以先参考[这里](https://linux.cn/article-13726-1.html)，毕竟笔者不一定写😁。

