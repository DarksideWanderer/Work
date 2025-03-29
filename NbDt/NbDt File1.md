## Ubuntu 22.04.5 LTS Server(Jammy Jellyfish)

Newbie Development Team(by darkside)

### 0x00 目标

目标是完成以下需求

1. 用其他系统连接ubuntu的终端,文件
2. 通过vscode完成远程连接

约定

1. 命令中的 `<>` 请省略
2. 教程基于 `windows10` `macos15`

### 0x01 下载虚拟机

| windows/linux | macos |
|-|-|
|[VMware Workstation Pro(17.6.3)](https://support.broadcom.com/group/ecx/free-downloads)|[VMware Fusion(13.6.3)](https://support.broadcom.com/group/ecx/free-downloads)|

需要注册 `broadcom.com` 的账号才能下载 .

以 `windows` 为例

无脑安装之后必须确认 `控制面板\网络和 Internet\网络连接` 存在两个虚拟网卡 `VMnet1\VMnet8`.

若没有,请另外搜索教程重装虚拟网卡.

### 0x02 下载ubuntu server镜像

[Server install image](https://releases.ubuntu.com/22.04/?_ga=2.66126574.1743464471.1743004956-534926283.1742729942&_gl=1*y37m2p*_gcl_au*ODI4MDU4MTExLjE3NDI5MTY5Njc.)

下载完成后应该会得到一个 `.iso` 文件 , 用 `vmware workstation/fusion(创建新的虚拟机->选择.iso文件)` 安装即可 , 语言选择 **English** , 一切按默认流程进行

### 0x03 局域网内 ssh

`ubuntu` 安装完之后,应该是一个类似于终端的黑框框,这是因为我们没有下载图形化界面 (我们也不需要图形化界面) .

1. 在终端中输入以下命令安装 `ssh` (虚拟机上)
   
   ```bash
   sudo apt update # 更新软件包
   sudo apt install openssh-server # 安装openssh服务器
   ```

   安装完成后, `ssh` 服务会自动启动.

2. 检查 `ssh` 服务状态
   
   安装完成后,可以通过以下命令检查ssh服务是否正在运行:

   ```bash
   sudo systemctl status ssh
   ```
   
   根据您的运行状态 , 可以通过以下指令控制 :

   ```bash
   sudo systemctl start ssh # 启用ssh服务
   sudo systemctl restart ssh # 重新启用ssh服务
   sudo systemctl stop ssh # 停止ssh服务
   sudo systemctl enable ssh # 开机自启动
   sudo systemctl disable ssh # 禁用开机自启动
   ```

   不只是 `ssh` , 服务有关的状态都可以通过以上命令控制 .

   ```bash
   sudo systemctl list-units --type=service # 列举所以已加载的服务和状态
   ```
   
3. 使用 `ssh` (宿主机上) 
   
   > `windows` 一般都会自带`ssh`客户端,如果没有:
   > 
   > (设置->系统->可选功能->添加功能->OpenSSH 客户端).
   >
   > 或者寻求其他帮助
   
   用管理员运行 `Power shell` 或者 `Terminal` .

   输入以下命令 :

   ```bash
   ssh <username>@<servername>
   ```

   其中 `username` 是你的用户名 , `servername` 是你服务器的名字 或 服务器的 IP . 然后你就会发现你的终端前面一串的名字变了,说明 `ssh` 成功 .

### 0x04 设置密钥

此部分来自 [@Zeddm](https://blog.csdn.net/h1008685/article/details/137458453)

软件 [PuTTYgen](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

> 1. 密钥对生成:
>    - 就像你拥有一个邮箱,你需要一把钥匙来打开它.在加密世界中,这把钥匙分为两部分:一把可以复制并随意分发的公钥,和一把只有你知道的私钥.
>    - 你使用特定的软件或命令生成一对密钥,比如使用`ssh-keygen`生成`ssh`密钥对.
>   
> 2. 公钥分享:
>    - 你将公钥分享给需要与你安全通信的人,就像你告诉朋友你的邮箱地址,他们可以给你发送信件.
>    - 在现实生活中,这可以通过电子方式(如电子邮件)或物理方式(如写在纸上)进行.
>  
> 3. 信息加密:
>    - 当别人想要给你发送加密信息时,他们使用你的公钥来加密信息,确保只有拥有对应私钥的你能够解密.
>    - 类似地,如果你收到一封加密的信件,你需要用你的私钥(邮箱钥匙)来打开它.
>  
> 4. 信息解密:
>    - 你使用私钥解密收到的信息,恢复原始内容.
>    - 就像你用钥匙打开邮箱,取出里面的信件.
>
> 5. 数字签名:
>    - 为了证明信息确实是你发送的,你可以使用私钥对信息进行数字签名.
>    - 类似地,你给信件签名,收信人通过你的公钥验证签名,就像验证信件上的签名是否为你的笔迹.
>  
> 6. 签名验证:
>    - 收信人使用你的公钥来验证数字签名,确认信息的真实性和完整性.
>    - 就像收信人通过对比签名和已知的你的笔迹来确认信件确实是你写的.

我们可以利用步骤 4 , 如果服务器仅允许通过与公钥匹配的私钥进行登录,那么就能有效防止他人通过暴力破解的方式尝试破解我们的密码,从而大大增强账户的安全性.

1. 生成密钥 `cmd/Terminal` (宿主机上)
   
   ```bash
   ssh-keygen -t rsa -b 4096 -C "这里是注释,随意填写"
   ```
   
   这个命令会生成一个 `4096` 位的 `RSA` 密钥对.
   
   你需要 指定密钥保存位置 ( 否则下载到系统默认位置 ), 设置密钥密码 ( 可选 )  

   之后的内容基于系统默认位置 , 有些需要修改路径 (`windows` 可以直接使用惯常的路径格式) , 后不再提醒 .

2. 将公钥上传到服务器  `Power shell/Terminal`
   
   > 对于 `windows` , 默认不支持 `ssh-copy-id` 命令 , 请在 `Power shell` 中运行如下脚本
   > 
   > ```powershell
   >function ssh-copy-id([string]$userAtMachine, $args) {
   >    $publicKey = "$ENV:USERPROFILE" + "/.ssh/id_rsa.pub" # Path
   >    if (!(Test-Path "$publicKey")) {
   >        Write-Error "ERROR: failed to open ID file '$publicKey': No such file"
   >    }
   >    else {
   >        & cat "$publicKey" | ssh $args $userAtMachine "umask 077; test -d .ssh || mkdir .ssh ; cat >> .ssh/authorized_keys || exit 1"
   >    }
   >}
   > ```
   >
   > 此脚本仅支持默认路径 , 请自行修改脚本

   输入命令 

   ```bash
   ssh-copy-id <user>@<hostname>
   ```

   这个命令会自动将公钥复制到远程服务器的 `~/.ssh/authorized_keys` 文件中.

3. 使用密钥进行 `ssh` 连接
   
   ```bash
   ssh -i ~/.ssh/id_rsa <user>@<hostname>
   ```

   如果提示输入密码,那就是你在创建密钥时的密码 .

4. 修改 `ssh` 配置文件禁止用户使用密码登陆 (虚拟机上)
   
   ```bash
   sudo vim /etc/ssh/sshd_config
   ```
   
   > 如果提示未安装 `vim` 用以下命令安装 :
   >
   > ```
   > sudo apt install vim
   > ```
   
   加入以下内容

   ```
   PasswordAuthentication no # 禁止ssh使用密码登陆
   ```

   然后重启 `ssh` 服务 .

最后达到的效果是,只能使用私钥登陆. 私钥文件可以转移到不同设备上,使其他设备也能连接到主机 .

### 0x05 使用 `sftp` 传输文件

软件 [Filezilla](https://filezilla-project.org/)

首先你需要用 `PuTTYgen(puttygen->load->Save private key)` 将你的 `rsa` 密钥转化为 `.ppk` 格式 .

然后用 `Filezilla(文件->站点管理器->新站点->常规{主机/端口(默认22)/登陆类型(密钥文件)}`

现在你就可以方便地和虚拟机交换文件了 .