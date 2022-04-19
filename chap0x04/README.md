# 实验环境
 - virtualBox 6.1
 - Ubuntu 20.04.02 Server
 - Travis Cl
 - VsCode (已经配置好相关环境)

# 实验内容
#### 任务一：用bash编写一个图片批处理脚本，实现以下功能：
 - √ 支持命令行参数方式使用不同功能
 - √ 支持对指定目录下所有支持格式的图片进行批处理指定目录进行批处理
 - √ 支持以下常见图片批处理功能的单独使用或组合使用
   -  支持对jpeg格式图片进行图片质量压缩
   -  支持对jpeg/png/svg格式图片在保持原始宽高比的前提下压缩分辨率
   -  支持对图片批量添加自定义文本水印
   -  支持批量重命名（统一添加文件名前缀或后缀，不影响原始文件扩展名）
   -  支持将png/svg图片统一转化为jpg格式
#### 任务二：用bash编写一个文本批处理脚本，对以下附件分别进行批量处理完成相应的数据统计任务：
 - √统计不同年龄区间范围（20岁以下、[20-30]、20岁以上）的球员数量、百分比
 - √统计不同长上位置的球员数量、百分比
 - √名字最长的球员是谁？名字最短的球员是谁？
 - √年龄最大的球员是谁？年龄最小的球员是谁？
#### 任务三：用bash编写一个批处理脚本，对附件分别进行批处理完成相应的数据统计任务

# 实验要求
- 所有源代码必须单独提交并提供详细的`-help`脚本内置帮助信息
- 任务三的所有统计数据结果写入独立实验报告

# 实验详情
## 课堂跟练
 - 变量
 ```
 #!/usr/bin/env bash
 BIRTHDATE="Jan 1 2000"   # 填入一个字符串
 Presents=10    # 填入一个整数
 BIRTHDAY="Saturday"    # 使用命令替换方法赋值
 ```

 - 数组
 ```
 NUMBERS=$(seq 1 10)  # 构造包含1到10整数的数组
 STRINGS=("hello" "world")  # 构造分别包含hello和world字符串的数组
 NumberOfNames=${#NAMES[@]} # 请使用动态计算数组元素个数的方法
 second_name='Eric'  # 读取NAMES数组的第2个元素值进行赋值
 ```

### 以下代码执行情况可查看[我的Travis Cl]()
#### 任务一
 - 安装`imagemagick`和`shellcheck`，并用远程从本地上传所需要的图片文件
 ```
 sudo apt-get update
 sudo apt-get install -y shellcheck
 sudo apt-get install imagemagick
 ```
 [![asciicast](https://asciinema.org/a/RjZKHCWQsTx8fzquU0MmExXXN.svg)](https://asciinema.org/a/RjZKHCWQsTx8fzquU0MmExXXN)

  -编写任务一脚本[task01.sh](shell\task1.sh)
* 任务一的思考：
```
对于某些特定的类型图像的处理需要判断格式，因为有些文件更改了后缀名之后会迷惑我们，此时有以下两种方法可以处理这种情况：
1.读取文件魔数（文件没有魔数的情况除外）
2.使用magick的命令`indentify -format "%m" 文件名`
```
![task1-output](img\task1-output.png)
* 一个不足：没有将操作后的图片另存，导致操作覆盖了上次操作的结果，又因为好不容易做出了太激动了也忘记截图了😥

#### 任务二
 - 将所需要的文件下载到本地
 ```
 wget "https://c4pr1c3.gitee.io/linuxsysadmin/exp/chap0x04/worldcupplayerinfo.tsv"
 ```
  -编写任务二脚本[task02.sh]shell\task2.sh)
* 注意：
```
要考虑并列的情况
```
![task2-outp](img\task2-output.png)

#### 任务三
 - 准备：安装好`p7zip-full`
 ```
 sudo apt-get install p7zip-full
 ```
 - 解压到本地
 ```
 wget "https://c4pr1c3.github.io/LinuxSysSdmin/exp/chap0x04/web_log.tsv.7z"
 ```
  -编写任务三脚本[task03.sh](shell\task3.sh)
 - 在任务三的完成中链接获取附件时失败，显示`404 not found`，故没有数据进行操作，只能到图片所示的程度。
![task3-output](img\task3-output.png)
 

# 参考资料
- [shell - 参数解析三种方式](https://bummingboy.top/2017/12/19/shell%20-%20%E5%8F%82%E6%95%B0%E8%A7%A3%E6%9E%90%E4%B8%89%E7%A7%8D%E6%96%B9%E5%BC%8F%28%E6%89%8B%E5%B7%A5,%20getopts,%20getopt%29/)
- [Travis Ci的使用](https://jianshu.com/p/bb0d91e42ac6)
- [bash和sh的区别](https://blog.csdn.net/houxiaoni01/article/details/105161356)
- [tsv文件概述](https://www.reviversoft.com/zh-cn/file-extensions/tsv)
- [wget使用方法](https://www.jb51.net/LINUXjishu/86326.html)
- [2021年kal1x师哥or师姐的实验](https://github.com/CUCCS/2021-linux-public-kal1x/blob/chap0x04/chap0x04/%E7%AC%AC%E5%9B%9B%E6%AC%A1%E5%AE%9E%E9%AA%8C.md)
- [markdown渲染失败的解决](http://markdownpad.com/faq.html#simultaneousinstallations)