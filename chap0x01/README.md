# 1.实验环境的记录

## 当前Linux发行版基本信息

### 查看版本信息有多种方式，命令'uname -a'显示电脑和Linux系统相关信息，即可查看内核版本信息

### 输入命令'lsb_release -a'可查看到电脑版本、Linux版本以及id等信息，可见**Description：Ubuntu 20.04.2 LTS**，版本信息是**20.04**

### 输入命令'cat/ect/issue'才是显示的发行版本的信息

## 当前Linux内核版本信息
### 输入命令'cat/proc/version'可查看到正在运行的Linux内核版本是**5.4.0**
### 以上信息如图所示：![版本信息](Screenshot\version-information.png)


--
# 2.Virtualbox安装完Ubuntu之后新添加的网卡如何实现系统开机自动启动和自动获取IP？

### 通过命令'ifconfig -a'可以看到全部的网卡，但有些网卡可能没有工作，说明没有出现的网卡就是没有生效的，所有网卡如图所示：![所有网卡](Screenshot\all-netcard.png)

### 通过命令'ifconfig'可以查看到工作网卡，工作网卡如图所示：![工作网卡](Screenshot\work-netcard.png)

### 系统使用**netplan**管理网络，vim打开文件'sudo vim /ect/netplan/00-installer-config.yaml'看到网卡是否开启dhch地址分配，没有则需要手动加入。最后执行'sudo netplan apply'生效。



--
# 3.scp命令传输文件

### 在虚拟机Linux环境下将文件传输给window宿主机：'scp administrator@192.168.56.1:/c:/test/config.ips/orecle'
### 将Linux中文件传输给window：'scp/oracle/yjm.txt administrator@192.168.56.1:/c:/'


--
# 4.配置ssh免密登录
### ①准备工作：首先确认宿主机和虚拟机的IP地址，并启动ssh服务
### ②输入命令'ssh-keygen -t rsa'生成密钥对，并将公钥传到虚拟机在
### ③宿主机上重启ssh服务后即可免密登录虚拟机了

# 参考文献：
### https://ubuntu.com/server/docs/network-configuration
### https://www.linuxcool.com/