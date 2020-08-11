# 远程linux免密登陆

本地机器是macOS，远程机器是linux的虚拟机，配置的ssh端口不是22所以还有些区别。免密登陆远程主机需要把本地主机生成的公钥放到远程主机中

## 傻瓜操作

1. 在本地机器上使用`ssh-keygen`命令生成公钥和私钥，在家目录下的`.ssh`文件夹中。

   ```shell
   ➜  .ssh ls
   config      id_rsa      id_rsa.pub  known_hosts
   ```

   Id_rsa为私钥，id_rsa.pub为公钥。

2. 将生成的公钥复制到远程机器上，这里选择使用scp命令。

   `scp 源文件路径 目标用户名@目标主机号：目标位置`

3. 将复制过去的id_rsa.pub添加到远程主机家目录下的.ssh文件夹中的`authorized_keys`文件中

   ```shell
   cat id_rsa.pub >> authorized_keys 
   ```

4. 不知道是否因为我连接的端口不是默认的22的原因，在进行了上述操作后不太成功，我在本地主机的`.ssh`文件夹下创建了`config`文件，为远程主机设置别名和一些配置。

   ```shell
   Host *远程主机别名
           HostName *远程主机IP
           User *远程主机用户
           Port *远程主机端口
           IdentityFile *本地私钥的路径
   ```

经过上述操作后`ssh *远程主机别名`就可成功连接。

## 知识点

