# 第二次实验报告

# #Ubuntu环境
## 1.【软件包管理】在目标发行版上安装 tmux 和 tshark ；查看这 2 个软件被安装到哪些路径；卸载 tshark ；验证 tshark 卸载结果

### 使用的命令如下
'''
sudo apt install tmux // 安装tmux
dpkg -L tmux // 查看tmux安装路径
sudo apt --purge remove tshark // 卸载tshark
'''

### 录屏结果如下：
 [![asciicast](https://asciinema.org/a/uRAPLq66KO6Fk3Q6G4QWdaezo.svg)](https://asciinema.org/a/uRAPLq66KO6Fk3Q6G4QWdaezo)


## 2.【文件管理】复制以下 shell 代码到终端运行，在目标 Linux 发行版系统中构造测试数据集

### 使用命令如下
'''
sudo find / -name "*666*" // 找到 /tmp 目录及其所有子目录下，文件名包含 666 的所有文件
find . | xargs grep "666" // 找到 /tmp 目录及其所有子目录下，文件内容包含 666 的所有文件
'''

### 录屏结果如下：
[![asciicast](https://asciinema.org/a/148INh6k7tS90ftrQ5fYTcRsh.svg)](https://asciinema.org/a/148INh6k7tS90ftrQ5fYTcRsh)

## 3.【文件压缩与解压缩】练习课件中 文件压缩与解压缩 一节所有提到的压缩与解压缩命令的使用方法

### 使用命令如下
#### gzip方式
'''
sudo apt-get install gzip // 安装gzip
gzip test // 压缩
ls
gzip -d test.gz // 解压缩
'''
#### bzip2方式
'''
sudo apt-get install bzip2 // 安装bzip2
bzip2 test // 压缩
ls
bzip2 -d test.bz2 // 解压缩
'''
#### zip方式
'''
sudo apt-get install zip // 安装zip
ls
zip -r text.zip test // 压缩
sudo apt-get install unzip // 安装unzip
rm -f test
unzip text.zip // 解压缩
'''
#### tar方式
'''
sudo apt-get install tar // 安装tar
tar -zcvf log.tar.gz test // 压缩
ls
rm -f test
tar -zxvf log.tar.gz // 解压缩
'''
#### rar方式
'''
sudo apt-get install rar // 安装rar
rar a text.rar test // 压缩
ls
rm -f test
rar e text.rar // 解压缩
'''

### 录屏结果如下：
[![asciicast](https://asciinema.org/a/ojRGAiC8NymFPbeuJjsQH5EI8.svg)](https://asciinema.org/a/ojRGAiC8NymFPbeuJjsQH5EI8)

## 4.【硬件信息获取】目标系统的 CPU、内存大小、硬盘数量与硬盘容量

### 查看cpu
使用命令如下：
'''
lscpu
cat /proc/cpuinfo
'''
### 查看内存
使用命令如下：
'''
free 
free -m
cat /proc/meminfo
'''
### 查看硬盘
使用命令如下：
'''
df
df -h
'''

### 录屏结果如下：
[![asciicast](https://asciinema.org/a/98TihCjWYzns9FikDoiJNmjiD.svg)](https://asciinema.org/a/98TihCjWYzns9FikDoiJNmjiD)

# #CentOS环境
### 1.【软件包管理】在目标发行版上安装 tmux 和 tshark ；查看这 2 个软件被安装到哪些路径；卸载 tshark ；验证 tshark 卸载结果

### 使用的命令如下
安装
'''
yum install tmux // 安装tmux
yum install wireshark // 安装tshark
'''
查看
'''
rpm -ql tmux // 查看tmux安装路径
rpm -ql wireshark // 查看tshark安装路径
'''
卸载及验证
'''
yum remove wireshark // 卸载tshark
wireshark -v
rpm -qa|grep wireshark // 验证卸载结果
'''

### 录屏结果如下：
[![asciicast](https://asciinema.org/a/FNcAwhOiXf0Y6CQXij6jk9nGH.svg)](https://asciinema.org/a/FNcAwhOiXf0Y6CQXij6jk9nGH)

## 2.【文件管理】复制以下 shell 代码到终端运行，在目标 Linux 发行版系统中构造测试数据集

### 使用命令如下
'''
sudo find / -name "*666*" // 找到 /tmp 目录及其所有子目录下，文件名包含 666 的所有文件
find . | xargs grep "666" // 找到 /tmp 目录及其所有子目录下，文件内容包含 666 的所有文件
'''

### 录屏结果如下：
[![asciicast](https://asciinema.org/a/i0RWl3uVlOLv42Q6mDD5qtXRc.svg)](https://asciinema.org/a/i0RWl3uVlOLv42Q6mDD5qtXRc)

## 3.【文件压缩与解压缩】练习课件中 文件压缩与解压缩 一节所有提到的压缩与解压缩命令的使用方法

### 使用命令如下
#### gzip方式
'''
yum install gzip // 安装gzip
gzip test // 压缩
ls
gzip -d test.gz // 解压缩
'''
#### bzip2方式
'''
yum install bzip2 // 安装bzip2
bzip2 test // 压缩
ls
bzip2 -d test.bz2 // 解压缩
'''
#### zip方式
'''
yum install zip // 安装zip
ls
zip -r text.zip test // 压缩
yum install unzip // 安装unzip
rm -f test
unzip text.zip // 解压缩
'''
#### tar方式
'''
yum install tar // 安装tar
tar -zcvf log.tar.gz test // 压缩
ls
rm -f test
tar -zxvf log.tar.gz // 解压缩
'''

### 录屏结果如下：
[![asciicast](https://asciinema.org/a/gEJlbF1ZljTlDb172Be3g6MJq.svg)](https://asciinema.org/a/gEJlbF1ZljTlDb172Be3g6MJq)

## 4.【硬件信息获取】目标系统的 CPU、内存大小、硬盘数量与硬盘容量

### 查看cpu
使用命令如下：
'''
lscpu
cat /proc/cpuinfo
'''
### 查看内存
使用命令如下：
'''
free 
cat /proc/meminfo
'''
### 查看硬盘
使用命令如下：
'''
df
df -h
'''
### 录屏结果如下：
[![asciicast](https://asciinema.org/a/eoPf45raIXYZp6KhVWvYaasvO.svg)](https://asciinema.org/a/eoPf45raIXYZp6KhVWvYaasvO)

# 实验表格总结
 | |Ubuntu|CentOS
 |----|----|----|
 |安装命令|sudo apt-get install 软件名|yum install 软件名
 |查看安装路径|dpkg -L 软件名|rpm -ql 软件名
 |删除命令|sudo apt-get --purge remove 软件名|yum remove 软件名
 |文件查找|两者都可使用sudo find / -name "*名称*" 和find . / xargs grep "内容"
 |压缩和解压缩|两者一致
 |查看cpu、内存和硬盘|两者一致
 





