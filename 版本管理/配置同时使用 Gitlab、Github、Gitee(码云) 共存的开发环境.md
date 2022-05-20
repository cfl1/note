## 确认版本

首先确认已安装Git，可以通过 `git –version` 命令可以查看当前安装的版本。

可以通过命令 `git clone https://github.com/git/git` 进行更新

Git共有三个级别的config文件，分别是`system、global和local`。

在当前环境中，分别对应

%GitPath%\mingw64\etc\gitconfig文件
 $home.gitconfig文件
 %RepoPath%.git\config文件

其中`%GitPath%`为Git的安装路径，`%RepoPath%`为某仓库的本地路径。

所以 system 配置整个系统只有一个，global 配置每个账户只有一个，而 local 配置和git仓库的数目相同，并且只有在仓库目录才能看到该配置。

**建立两个密钥，不同账号配置不同的密钥，不同仓库配置不同密钥。**

## 1. 更改 git 的全局设置（针对已安装 git）

新安装 git 跳过。

若之前对 git 设置过全局的 `user.name` 和 `user.email`。
用 `git config --global --list` 进行查看你是否设置

```bash
git config --global user.name "chenfl"
git config --global user.email  "1571539116@qq.com"
```

必须删除该设置

```bash
git config --global --unset user.name "chenfl"
git config --global --unset user.email "1571539116@qq.com"
```

## 2. 生成新的 SSH keys

### 1）GitHub 的钥匙

指定文件路径，方便后面操作：`~/.ssh/id_rsa.gitlab`

```bash
ssh-keygen -t rsa -f ~/.ssh/id_rsa.github -C "1571539116@qq.com"
```

直接回车3下，什么也不要输入，就是默认没有密码。

注意 github 和 gitlab 和 gitee 的文件名是不同的。

2）GitLab 的钥匙

```bash
ssh-keygen -t rsa -f ~/.ssh/id_rsa.gitlab -C "1571539116@qq.com"
```

### 2）Gitee 的钥匙

```bash
ssh-keygen -t rsa -f ~/.ssh/id_rsa.gitee -C "1571539116@qq.com"
```

### 3)完成后会在~/.ssh / 目录下生成以下文件

- id_rsa.github
- id_rsa.github.pub
- id_rsa.gitlab
- id_rsa.gitlab.pub

------

## 3.添加识别 SSH keys 新的私钥

默认只读取 id_rsa，为了让 SSH 识别新的私钥，需要将新的私钥加入到 SSH agent 中

```bash
ssh-agent bash
ssh-add ~/.ssh/id_rsa.github
ssh-add ~/.ssh/id_rsa.gitlab
ssh-add ~/.ssh/id_rsa.gitee
```

## 4. 多账号必须配置 config 文件(重点)

若无 config 文件，则需创建 config 文件

### 创建config文件

```bash
touch ~/.ssh/config    
```

### config 里需要填的内容如下

```tsx
#Default gitHub user Self
Host github.com
    HostName github.com
    User git 
    IdentityFile ~/.ssh/id_rsa.github

#Add gitLab user 
    Host git@gitlab.com
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_rsa.gitlab

# gitee
Host gitee.com
    Port 22
    HostName gitee.com
    User git
    IdentityFile ~/.ssh/id_rsa.gitee


# 其他自己搭建的
Host git@git.startdt.net
    Port 22
    HostName http://git.startdt.net
    User git
    IdentityFile ~/.ssh/lab_rsa.startdt
```

下面对上述配置文件中使用到的配置字段信息进行简单解释：

- Host
   它涵盖了下面一个段的配置，我们可以通过他来替代将要连接的服务器地址。
   这里可以使用任意字段或通配符。
   当ssh的时候如果服务器地址能匹配上这里Host指定的值，则Host下面指定的HostName将被作为最终的服务器地址使用，并且将使用该Host字段下面配置的所有自定义配置来覆盖默认的/etc/ssh/ssh_config配置信息。
- Port
   自定义的端口。默认为22，可不配置
- User
   自定义的用户名，默认为git，可不配置
- HostName
   真正连接的服务器地址
- PreferredAuthentications
   指定优先使用哪种方式验证，支持密码和秘钥验证方式
- IdentityFile
   指定本次连接使用的密钥文件

## 5.在 github 、 gitlab、gitee 网站添加 ssh

### Github

![Snipaste_2021-12-11_22-45-47]( https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E7%89%88%E6%9C%AC%E7%AE%A1%E7%90%86/%E9%85%8D%E7%BD%AE%E5%90%8C%E6%97%B6%E4%BD%BF%E7%94%A8%20Gitlab%E3%80%81Github%E3%80%81Gitee%28%E7%A0%81%E4%BA%91%29%20%E5%85%B1%E5%AD%98%E7%9A%84%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83/img/Snipaste_2021-12-11_22-45-47.jpg)

Github 添加SSH公钥

直达地址：[https://github.com/settings/keys](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fsettings%2Fkeys)

过程如下：

1. 登录 Github
2. 点击右上方的头像，点击 `settings`
3. 选择 `SSH key`
4. 点击 `Add SSH key`

在出现的界面中填写 SSH key 的名称，填一个你自己喜欢的名称即可。
 将上面拷贝的`~/.ssh/id_rsa.xxx.pub`**文件全部内容**粘帖到 key 一栏，在点击 “add key” 按钮就可以了。

添加过程 github 会提示你输入一次你的 github 密码 ，确认后即添加完毕。

### Gitlab

![Snipaste_2021-12-11_22-46-09]( https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E7%89%88%E6%9C%AC%E7%AE%A1%E7%90%86/%E9%85%8D%E7%BD%AE%E5%90%8C%E6%97%B6%E4%BD%BF%E7%94%A8%20Gitlab%E3%80%81Github%E3%80%81Gitee%28%E7%A0%81%E4%BA%91%29%20%E5%85%B1%E5%AD%98%E7%9A%84%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83/img/Snipaste_2021-12-11_22-46-09.jpg)

Gitlab  添加SSH公钥

直达地址：[https://gitlab.com/profile/keys](https://links.jianshu.com/go?to=https%3A%2F%2Fgitlab.com%2Fprofile%2Fkeys)

1. 登录 Gitlab
2. 点击右上方的头像，点击 `settings`
3. 后续步骤如 Github

### Gitee / 码云

![Snipaste_2021-12-11_22-46-51]( https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E7%89%88%E6%9C%AC%E7%AE%A1%E7%90%86/%E9%85%8D%E7%BD%AE%E5%90%8C%E6%97%B6%E4%BD%BF%E7%94%A8%20Gitlab%E3%80%81Github%E3%80%81Gitee%28%E7%A0%81%E4%BA%91%29%20%E5%85%B1%E5%AD%98%E7%9A%84%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83/img/Snipaste_2021-12-11_22-46-51.jpg)

码云 添加SSH公钥

直达地址：[https://gitee.com/profile/sshkeys](https://links.jianshu.com/go?to=https%3A%2F%2Fgitee.com%2Fprofile%2Fsshkeys)

1. 登录 Gitee
2. 点击右上方的头像，点击 `设置`
3. 后续步骤如 Github

添加过程 码云 会提示你输入一次你的 Gitee 密码 ，确认后即添加完毕。

## 6.测试是否连接成功

由于每个托管商的仓库都有唯一的后缀，比如 Github 的是 [git@github.com](https://links.jianshu.com/go?to=mailto%3Agit%40github.com):*。

所以可以这样测试：

```bash
ssh -T git@github.com
ssh -T git@gitlab.com
ssh -T git@gitee.com
```

 如果能看到一些 Welcome 信息，说明就是 OK 的了,如:

```ruby
chenfl@DESKTOP-VFEC2HJ MINGW64 ~/.ssh
$ ssh -T git@github.com
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ECDSA key fingerprint is SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com,20.205.243.166' (ECDSA) to the list of known hosts.
Hi cfl1! You've successfully authenticated, but GitHub does not provide shell access.

chenfl@DESKTOP-VFEC2HJ MINGW64 ~/.ssh
$ ssh -T git@gitee.com
The authenticity of host 'gitee.com (180.97.125.228)' can't be established.
ECDSA key fingerprint is SHA256:FQGC9Kn/eye1W8icdBgrQp+KkGYoFgbVr17bmjey0Wc.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'gitee.com,180.97.125.228' (ECDSA) to the list of known hosts.
Hi chenfl! You've successfully authenticated, but GITEE.COM does not provide shell access.
```

结果如果出现这个就代表成功：

- GitHub -> successfully
- GitLab -> Welcome to GitLab
- Gitee -> successfully

## 7.其他说明

有的时候github拉去速度十分慢可以使用国内镜像，目前已知Github国内镜像网站有[github.com.cnpmjs.org](https://github.com.cnpmjs.org/)和[git.sdut.me/](https://git.sdut.me/)。速度根据各地情况而定，在clone某个项目的时候将github.com替换为github.com.cnpmjs.org即可。