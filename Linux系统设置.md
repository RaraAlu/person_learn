#### 第一步

```shell
1>改网络为桥接模式
2>切换阿里源
sudo apt update && sudo apt upgrade -y
3>安装 open-vm-tools
sudo apt-get install open-vm-tools*   -----之后  reboot
sudo apt install openssh-server -y
sudo apt install vim
sudo apt install tree
>配置 platformIO IDE 的python环境
sudo apt-get install python3-venv
=============================================
cd .platformio/penv && vi pip.conf
[global]
user=no
index-url = https://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host = mirrors.aliyun.com
=============================================
>卸载干扰USB的包
sudo apt remove brltty
>更换终端
sudo apt-get install terminator

wget http://fishros.com/install -O fishros && . fishros
>启动代理
.clash/start_clash.sh
```

#### vim

```
https://zhuanlan.zhihu.com/p/538259855
```

#### 更改用户名工

不要登陆已有的账号，在输入密码的界面, `Ctrl+F3`开启一个新的终端

```shell
1>修改用户名
	usermod -l 新用户  旧用户 
2>修改组名
	groupmod -n 新用户名 旧用户名
3>修改UID
	sudo usermod -u 1000 新用户名
```

#### Git_SSH

```shell
Vscode ->config---文件
Host farsight
    HostName 192.168.40.15
    User farsight
    IdentityFile ~/.ssh/id_rsa_ip1
Host ls
    HostName 192.168.31.2
    User ls
    IdentityFile ~/.ssh/ls_rsa
    
windows c盘用户 .ssh 删除公钥和私钥后执行
>生成密钥对(ls_key)可自定义---指定绝对路径 实际不可添加括号
ssh-keygen -t rsa -b 4096 -f "C:\Users\陈\.ssh\ls_key"

>将 SSH 公钥复制到远程主机的授权文件中 ---指定绝对路径
ssh-copy-id -i "C:\Users\陈\.ssh\ls_key.pub" ls@192.168.31.6
```

#### Samba

```shell
linux@ubuntu:~$ sudo apt-get install samba samba-common
linux@ubuntu:~$ sudo vi /etc/samba/smb.conf
[ls]
path = /home/ls/ls
available = yes
browseable = yes
writable = yes
valid users = ls

[home]
path = /home/ls
available = yes
browseable = yes
public = yes
writable = yes

[opt]
path = /opt
available = yes
browseable = yes
public = yes
writable = yes
//配置samb用户名密码和ubuntu相同
sudo smbpasswd -a ls
//重启生效
sudo /etc/init.d/smbd reload
sudo /etc/init.d/smbd restart
```

#### 查找指令

`find`

`grep`

`locate：`使用之前需updatedb更新系统数据库

#### 配置静态 IP

```shell
3.配置网络IP和DNS
sudo vim /etc/netplan/01-network-manager-all.yaml
复制：
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    ens33:
      dhcp4: no
      addresses: [192.168.31.6/24]
      routes:
        - to: 0.0.0.0/0
          via: 192.168.31.1
      nameservers:
        addresses: [114.114.114.114, 8.8.8.8]
        
        
network:  
  version: 2  
  renderer: networkd  
  ethernets:  
    eth0:  
      addresses: [192.168.31.8/24]  
      gateway4: 192.168.31.1  
      nameservers:  
          addresses: ['8.8.8.8', '8.8.4.4']

最后应用 sudo netplan apply 
```

#### 垃圾清理

```shell
sudo apt-get autoremove --purge
sudo apt-get autoclean
```

#### 网络重置

```shell
>
sudo apt install net-tools

sudo ifconfig ens33 down
sudo ifconfig ens33 up
sudo dhclient ens33
```

在Ubuntu 22.04中，可以使用以下指令来进行解压和压缩操作：

1.  **解压缩 .zip 文件**：

    ```shell
    unzip file.zip
    ```

2.  **解压缩 .tar 文件**：

    ```
    tar -xvf file.tar
    ```

3.  **解压缩 .tar.gz 或 .tgz 文件**：

    ```
    tar -xzvf file.tar.gz
    ```

4.  **解压缩 .tar.bz2 文件**：

    ```
    tar -xjvf file.tar.bz2
    ```

5.  **解压缩 .tar.xz 文件**：

    ```
    tar -xJvf file.tar.xz
    ```

6.  **解压缩 .rar 文件**（需要安装 unrar）：

    ```
    unrar x file.rar
    ```

7.  **解压缩 .7z 文件**（需要安装 p7zip-full）：

    ```
    7z x file.7z
    ```

8.  **压缩为 .zip 文件**：

    ```
    zip -r archive.zip folder/
    ```

9.  **压缩为 .tar 文件**：

    ```
    tar -cvf archive.tar folder/
    ```

10.  **压缩为 .tar.gz 文件**：

     ```
     tar -czvf archive.tar.gz folder/
     ```

#### 一键安装

```shell
wget http://fishros.com/install -O fishros && . fishros
```

#### deb安装器

```shell
sudo apt install gdebi
```

#### windows置顶

```
Window TopMost Control
```

#### clashX地址

```
https://s.trojanflare.com/clashx/efdab3fa-4472-48cf-89a2-72e090afa0aa
```

打开Clash for Windows软件：
● General：开启 Mixin、System Proxy、Start with Windows
● Proxies：上方选择 Global，下面选择 Auto
● Profiles：拷贝-> https://s.trojanflare.com/clashx/efdab3fa-4472-48cf-89a2-72e090afa0aa -> 点击Download 
● General：点击上方 Clash for Windows
● Proxies：关闭网络-点击 config.yaml
● 访问地址示例：https://github.com/damuxt/ros2_arduino_bridge
