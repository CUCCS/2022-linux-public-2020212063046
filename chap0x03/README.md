# 第三次实验报告
## 【实验环境】
 virtualbox
 Ubuntu 20.04.02 server 
## 【实验内容】
 跟练Systemd入门教程：命令篇+实战篇
 自查清单
## 【实验详情】
### 一、跟练systemd入门教程：命令篇
 #### 1.linux 启动
 #### 2.查看systemd版本
 '''
 systemctl --version #查看版本信息
 '''
 显示的信息是==systemd 245 (245.4-4ubuntu3.4)==
 #### 3.系统管理
 ##### 3.1 systemctl管理系统命令组
 • sudo systemctl reboot #重启系统
 • sudo systemctl poweroff #关闭系统并切断电源
 • sudo systemctl halt #cpu停止
 • sudo systemctl suspend #暂停系统
 • sudo systemctl hibernate #系统进入睡眠状态
 • sudo systemctl rescue #启动进入救援状态
 
 ##### 3.2 systemd-analyze查看启动耗时
'''
 systemd-analyze #查看启动耗时
 systemd-analyze blame #查看每个服务器启动耗时
 systemd-analyze critical-chain #显示瀑布状的启动过程流
 systemd-analyze critical-chain atd.service #显示指定服务的启动流
 '''
 [![asciicast](https://asciinema.org/a/VWmwTin0Ah4VENTEzYrFXzfR9.svg)](https://asciinema.org/a/VWmwTin0Ah4VENTEzYrFXzfR9)

 ##### 3.3 hostnamectl查看当前主机信息
 '''
 hostnamectl #当前主机信息
 sudo hostnamectl set-hostname rhel7 #设置主机名
 '''
 ##### 3.4 localectl查看本地化设置
 '''
 localectl #查看本地化设置
 sudo localectl set-locale LANG=en_GB.utf8 #设置本地参数
 sudo localectl set-keymap en_GB #设置本地参数
 '''
 [![asciicast](https://asciinema.org/a/dtTObZ2IE0GzmcZPFRZaFHkQR.svg)](https://asciinema.org/a/dtTObZ2IE0GzmcZPFRZaFHkQR)

 ##### 3.5 timedatectl查看当前时区设置
 '''
 timedatectl #查看当前时区设置
 timedatectl list-timezones #显示所有可用时区
 sudo timedatectl set-timezone America/New_York #设置当前时区
 sudo timedatectl set-time YYYY-MM-DD #设置当前时区
 sudo timedatectl set-time HH:MM:SS #设置当前时区
 '''

 ##### 3.6 loginctl 查看当前登录用户
 '''
 loginctl list-sessions #列出当前session
 loginctl list-users #列出当前登录用户
 loginctl show-user ruanyf #列出指定用户信息
 '''
[![asciicast](https://asciinema.org/a/pW8jMMJmUNq4tK9k9STRi0AfO.svg)](https://asciinema.org/a/pW8jMMJmUNq4tK9k9STRi0AfO)

 #### 4.Unit
 ##### 4.1 含义
 • systemd可以管理所有系统资源，不同系统资源统称为unit，unit是一个单位，unit一个可分为12种。
 systemctl list-units查看当前系统所有unit
 '''
 systemctl list-units #列出正在运行的unit
 systemctl list-units --all #列出所有unit
 systemctl list-units --all --state=inactive #列出没有unit
 systemctl list-units --failed #列出加载失败unit
 systemctl list-units --type=service #列出所有正运行的切类型为service的unit
 '''
 [![asciicast](https://asciinema.org/a/1XzpblQCtdeGfLfaJQEn68hhA.svg)](https://asciinema.org/a/1XzpblQCtdeGfLfaJQEn68hhA)

 ##### 4.2 systemctl status查看状态
 '''
 systemctl status #显示系统状态
 systemctl status bluetooth.service #显示单个unit状态
 systemctl -H root@rhel7.example.com status httpd.service #显示远程主机上某个unit状态
 '''
 主要供脚本内部的判断语句使用的另外三种简单查询状态方法：
 '''
 systemctl is-active application.service
 systemctl is-failed application.service
 systemctl is-enabled application.service
 '''
 [![asciicast](https://asciinema.org/a/Ot2FE2NSRCB233vV4mbwOxE7Q.svg)](https://asciinema.org/a/Ot2FE2NSRCB233vV4mbwOxE7Q)

 ##### 4.3 unit管理
 '''
 sudo systemctl start apache2.service #立即启动服务器
 sudo systemctl stop apache2.service #立即停止服务器
 sudo systemctl restart apache2.service #重启服务器
 sudo systemctl kill apache2.service #杀死服务器的所有子进程
 sudo systemctl reload apache2.service #重新加载一个服务的配置文件
 sudo systemctl daemon-reload #重新加载修改过的配置文件
 systemectl show httpd.service #显示一个unit的底层参数
 systemectl show -p CPUShares httpd.service #显示一个unit的指定属性值
 sudo systemectl set-property httpd.service CPUShares=500 #设置某个unit的指定属性
 '''

 ##### 4.4 依赖关系
 A依赖于B：systemd在启动A时同时启动B。
 '''
 systemctl list-dependencies nginx.service #列出unit所有依赖
 '''
 注：上述命令输出结果中的target类型依赖默认不展开显示，要展开显示需要使用all参数
 '''
 systemctl list-dependencies -all nginx.service
 '''
 [![asciicast](https://asciinema.org/a/LXJpH9MQswcf1A0r9TxsHr8dl.svg)](https://asciinema.org/a/LXJpH9MQswcf1A0r9TxsHr8dl)

 #### 5.unit配置文件
 ##### 5.1 概述
 systemd默认目录里面存放的大部分文件是符号链接，指向真正的配置文件存放的目录
 '''
 sudo systemctl enable clamd@scan.service
 sudo ln -s '/usr/lib/systemd/system/clamd@scan.service' '/ect/systemd/system/muliti-user.target.wants/clamd@scan.service'
 '''
 撤销符号链接关系
 '''
 sudo systemctl disable clamd@scan.service
 '''

 ##### 5.2 配置文件状态
 '''
 systemctl list-unit-files #列出所有配置文件
 systemctl list-unit-files --type=service #列出指定配置文件
 '''
执行上述结果后可以看到每个配置文件的状态，这里显示的是四种：
 ① masked：配置文件禁止建立启动链接
 ② enable：已经建立启动链接
 ③ static：无法单独执行，只能作为其他配置文件的依赖
 ④ disabled：没有建立启动链接
 ==注==：从这里配置文件状态无法确定unit是否运行。要查看运行状态需要执行上文的systemectl status命令
 
 如果修改配置文件，需要重新加载切重启，否则修改不生效
 '''
 sudo systemctl deamon-reload
 sudo systemctl restart httpd.service
 '''
 [![asciicast](https://asciinema.org/a/ST4Tkdm0guogpTi1vlhervmXa.svg)](https://asciinema.org/a/ST4Tkdm0guogpTi1vlhervmXa)

 ##### 5.3 配置文件格式
 配置文件就是普通文本文件，直接使用文本编辑器打开即可
 '''
 systemctl cat atd.service

 [Unit]
 Description=ADT deamon

 [Service]
 Type=forking
 ExecStart=/usr/bin/atd

 [Install]
 WantedBy=multi-user.target
 '''
 [![asciicast](https://asciinema.org/a/X4Ep6h57kV094bi8v6f9dhNQm.svg)](https://asciinema.org/a/X4Ep6h57kV094bi8v6f9dhNQm)

 #### 6.Target
 解决启动计算机时一一写明需要启动unit的麻烦，使用target来标记"状态点"
 '''
 systemctl list-unit-files --type=target #查看所有target
 systemctl list-dependencies multi-user.target #查看target包含的unit
 systemctl get-default #查看启动默认target
 sudo systemctl set-default multi-user.target #设置启动默认target
 sudo systemctl isolate multi-user.target #关闭前一个target里不属于后一个target的进程
 '''
 [![asciicast](https://asciinema.org/a/2xd1057DfYxIdSwo9mRMG2gK0.svg)](https://asciinema.org/a/2xd1057DfYxIdSwo9mRMG2gK0)

 #### 7.日志管理
 '''
 sudo journalctl #查看所有日志
 sudo journalctl -k #查看内核日志
 sudo journalctl -b #查看系统此次启动日志
 sudo journalctl -b -0 #查看系统此次启动日志
 sudo journalctl -b -l #查看系统上次启动日志
 sudo journalctl --sence="2020-04-02 18:11:12" #查看指定时间日志
 sudo journalctl --sence "25 min ago" #查看指定时间日志
 sudo journalctl --sence yesterday #查看指定时间日志
 sudo journalctl --sence="2020-04-04" --until "2020-04-05 00:05" #查看指定时间日志
 sudo journalctl --sence 06:00 --until "3 hours ago" #查看指定时间日志
 sudo journalctl -n #查看最新的10条日志
 sudo journalctl -n 16 #查看指定行数日志
 sudo journalctl -f #实时显示最新日志
 sudo journalctl /usr/lib/systemd/systemd #查看特定服务日志
 sudo journalctl_PID=1 #查看特定进程日志
 suod journalctl /usr/bin/bash #查看特定路径脚本日志
 suod journalctl_UID=33 --since today #查看特定用户日志
 sudo journalctl -u nginx.service #查看特定unit日志
 sudo journalctl -u nginx.service --since today #查看特定unit日志
 sudo journalctl -u nginx.service -f #实时显示最新unit日志
 sudo journalctl -u nginx.service -u php-fpm.service --since today #合并显示多个unit日志
 sudo journalctl -p err -b #查看优先级日志
 sudo journalctl --no-pager #由默认分页输出改为标准输出
 sudo journalctl -b -u nginx.service -o json #以json格式单行输出
 sudo journalctl -b -u nginx.serviceqq -o json-pretty #以json格式多行输出
 sudo journalctl --disk-usage #显示日志占据的硬盘空间
 sudo journalctl --vacuum-size=1G #设定日志占据的最大空间
 suod journalctl --vaccum-time=6months #设定日志保存的时间
 '''
 [![asciicast](https://asciinema.org/a/I1bXw7GGyxtk5h0MTTI6H3eP1.svg)](https://asciinema.org/a/I1bXw7GGyxtk5h0MTTI6H3eP1)

### 二、自查清单
 #### 1.如何添加一个用户并使其具备sudo执行程序的权限？
 '''
 sudo adduser guest
 sudo usermod -G sudo -a guest
 '''
 ![添加用户赋予权限](img\添加用户赋予权限.png)

 #### 2.如何将一个用户添加到另一个用户组？
 '''
 sudo -- usermod -G lp guest
 '''
 ![添加用户](img\添加用户.png)
 #### 3.如何查看当前系统的分区表和文件系统详细信息？
 '''
 sudo fdisk -l #查看分区表
 df -h #查看目前磁盘使用空间情况
 '''
 ![分区表](img\分区表.png)
 ![系统详情](img\系统详情.png)
 #### 4.如何实现开机自动挂载virtualbox的共享目录分区？
 准备工作：宿主机创建名为“share”的共享文件夹并完成设置
          具体操作是：设备->共享文件夹->添加->指定路径
 虚拟机内新建共享文件挂载目录，外部驱动器目录挂载在此目录下
 '''
 cd /mnt/ 进入挂载目录
 sudo mkdir ubuntushares #创建共享文件夹
 '''
 执行挂载命令
 '''
 sudo mount -t vboxsf share /mnt/ubuntushares
 '''
 修改/ect/fstab文件添加share/mnt/ubuntushares/ vboxsf defaults 0 0完成挂载
 #### 5.基于LVM（逻辑分卷管理）的分区如何实现动态扩容和缩减扩容？
 '''
 fsidk XXX #分区XXX的剩余空间创建分区并改为LVM格式
 lvextend -L +<容量><目录> #扩容
 lverduce -L -<容量><目录> #减容
 '''
 #### 6.如何通过systemd设置实现一个脚本在任何情况下被杀死之后会立即重新启动？实现杀不死？
 修改配置文件[Service]区块，把==restart==修改为==always==
 '''
 [Service]
 Restart = Always
 '''
 重新加载配置文件
 '''
 sudo systemctl deamon-reload
 '''
 重启服务器 
 '''
 sudo systemctl reboot
 '''
 ==怎样确认为杀不死脚本？==
 '''
 sudo systemctl kill ssh.service
 sudo systemctl status ssh

 '''
 杀死ssh再次查看状态发现ssh仍然存在即成功

## 【参考文献】
https://m.xitongjiaocheng.com
https://www.cnblogs.com


 
 
 


