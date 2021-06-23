# 新系统配置

新系统设置和更换内核版本的记录

## SSH服务

```shell
sudo apt isntall openssh-server		#可能先需要更新apt update
sudo systemctl status ssh 				#验证服务是否运行
```

## 远程免密登陆

使用`ssh-keygen`在`.ssh`文件夹下生成公钥和私钥

```shell
#公钥复制到远程服务器上，添加到.ssh文件夹下的authorized_keys文件中
cat id_ras.pub >> authorized_keys

#.ssh文件夹下config文件设置
Host *远程主机别名
        HostName *远程主机IP
        User *远程主机用户
        Port *远程主机端口
        IdentityFile *本地私钥的路径
```

## 基本编译环境

```shell
sudo apt install build-essential
```

## 更换内核版本

```shell
uname -r #查看内核版本
apt-cache search linux-header/image #查看可安装的内核版本
sudo apt-get install linux-header-* linux-image-*	#安装一个iamge和一个header。？有人安装了extra
sudo update-grup	#更新grub
?设置grub默认启动内核
```

## 用户添加/删除/修改

root身份/sudo权限的用户

```shell
useradd/adduser
sudo adduser username 	#创建用户

sudo deluser username
sudo deluser --remove-home username 	#删除用户，是否删除家目录文件

hostnamectl set-hostname "new-hostname" 	#通过系统命令重命名主机
```

