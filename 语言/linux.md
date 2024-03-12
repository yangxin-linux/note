### 常用指令

#### 1.user

- `useradd -d /home/alpha alpha`    创建用户指定家目录

- `useradd -d  /home/aplha -g test alpha`  创建用户指定家目录，用户所在组

- `passwd alpha`   修改用户密码

- `userdel alpha`  删除用户

- `userdel -r user`  删除用户,并删除其家目录

- `id alpha`  查看用户信息  uid gid

- `su alpha`  切换到其他用户

- `usermod -g dev alpha`   修改用户所在组

- >/etc/passwd  存储用户的各种身份信息
  >
  >/etc/shadow  存储用户的登录口令信息(加密)  修改密码,登录时间等

#### 2.group

- `groupadd test`   创建用户组

- `groupdel test`    删除用户组

- > /etc/group  创建的所有组信息

#### 3.运行级别

- 0 关机
- 1 单用户  找回丢失的密码
- 2 多用户状态 没有网络服务
- 3 多用户有网络状态
- 4 未被使用
- 5 图形界面
- 6 系统重启

> 运行级别配置文件  /etc/inittab
>
> 切换运行级别  init 3

#### 4.重定向

- `ls -l  > 文件`  将内容输入文件中  覆盖操作
- `ls -al >> 文件`  将内容输入文件中  追加操作
- `cat 文件1 > 文件2`  将文件1内容覆盖到文件2中
- `cat 文件1 >> 文件2`  将文件1内容追加到文件2中
- `echo  信息  >  文件`    将信息两个字写入文件中

#### 5.echo head tail

- `echo $PATH`  输出当前的环境变量路径
- `head -n 5 文件路径`  显示文件前5行
- `tail -f 文件路径`   实时追至文件更新信息 如查看日志

#### 6.软/硬链接

- `ln -s 源文件/目录 软链接名`  创建软链接
- `rm -rf 软链接` 带/ 删除源目录/文件  不带/ 删除软链接

> **硬链接：**
>
> 文件存储在硬盘上，最小单位是扇区 sector，操作系统在硬盘上读取扇区上的数据，以8个连续的扇区为单位读取
>
> 8个连续的扇区称为块 block ，一个扇区0.5KB 一个块是4KB
>
> linux操作系统专门用于管理和存储文件的软件叫做INODE，文件系统
>
> 文件以文件元数据加文件元信息组成，文件的INODE编号加文件数据组成单个文件
>
> 文件系统又将文件元信息存储在一个inode区域的地方，中文叫做索引节点
>
> `stat /home/temp/a.txt` 查看文件元信息
>
> > stat /home/temp/a.txt
> >
> > 文件：a.sh
> >   大小：26        	块：8          IO 块大小：4096   普通文件
> > 设备：10305h/66309d	Inode：4587866     硬链接：1
> > 权限：(0664/-rw-rw-r--)  Uid: ( 1000/     hxh)   Gid: ( 1000/     hxh)
> > 访问时间：2023-05-08 05:55:56.031102659 +0800
> > 修改时间：2023-05-08 05:55:53.755147606 +0800
> > 变更时间：2023-05-08 05:55:53.767147369 +0800
> > 创建时间：2023-05-08 05:55:53.755147606 +0800
>
> 查看一个文件 先获取INODE号然后通过INODE号找到对应的BLOCK位置
>
> ---
>
> 软链接INODE号码是不一样的，而硬链接INODE号都一样，指向同一个文件
>
> 软链接可以指向目录，而硬链接不行
>
> 软链接可以跨文件系统，硬链接不行
>
> 软链接的删除对源文件无影响，硬链接的删除对源文件无影响
>
> 删除源文件对软链接有影响，对硬链接无影响
>
> 只有删除源文件和所有的硬链接，此时文件数据才丢失
>
> `ln /home/a.tx /home/aaa`  给a.txt文件创建硬链接

#### 7.history

- `history`

- `history 10`

- 自定义显示格式

  ```bash
  vim /etc/profile.d/history_conf.sh 
  #------------------------------------------------------
  export HISTFILE="$HOME/.bash_history_new"  # 指定命令写入文件(默认~/.bash_history)
   
  export HISTSIZE=1000  # history输出记录数
   
  export HISTFILESIZE=10000  # HISTFILE文件记录数
   
  export HISTIGNORE="cmd1:cmd2:..."  # 忽略指定cmd1,cmd2...的命令不被记录到文件；(加参数时会记录)
   
  export HISTCONTOL=ignoreboth    # ignoredups 不记录“重复”的命令；连续且相同 方为“重复” ；
                                  # ignorespace 不记录所有以空格开头的命令；
                                  # ignoreboth 表示ignoredups:ignorespace ,效果相当于以上两种的组合；
                                  # erasedups 删除重复命令；
   
  export PROMPT_COMMAND="history -a"  # 设置每条命令执行完立即写入HISTFILE(默认等待退出会话写入)
   
  export HISTTIMEFORMAT="`whoami` %F %T "  # 设置命令执行时间格式，记录文件增加时间戳(`whoami`用户信息)
   
  shopt -s histappend  # 防止会话退出时覆盖其他会话写到HISTFILE的内容；
  #------------------------------------------------------
  source /etc/profile.d/history_conf.sh 
  ```
  
  

#### 8.date

- date  显示当前时间
- date "+%Y"
- date  "+%m"
- date  "+%d"
- ` date "+%Y-%m-%d %H:%M:%S"` 指定格式显示时间
- `date -s "2021-08-15 6:17:50"`  设置时间

#### 9.find locate grep

- `find 查找路径  -name 文件名`  find /home -name a.txt  |  find /home -name *.txt
- `find 查找路径 -user 用户名`  查找指定路径下 某个用户拥有的文件/目录   find /home -user root
- `find 查找路径 -size 文件大小`  查找指定路径下大于10M的文件  find /usr/bin -size +10M
- `updatedb` 建立文件索引     `locate 文件名` 根据文件名查找文件
- grep 过滤查找  -i 忽略大小写  -n  显示行号 
- `cat a|grep bin -n -i`   查看文件a中的内容是否包含 bin  并显示行号 忽略大小写

#### 10.解压缩

- `gzip 文件名` 压缩文件   不会保留源文件

- `gunzip 文件名`  解压文件  只能处理 *.gz的文件

---

- `zip -r 文件目录`  -r 压缩整个目录

- `unzip -d 解压目录 文件目录`  解压文件  只能处理 *.zip的文件

---

- `tar -zcvf a.tar.gz a.txt b.jpg` 将a.txt b.jpg打包成a.tar.gz文件
- `tar -zcvf a.tar.gz /home`  将home目录下所有文件直接打包
- `tar -zxvf a.tar.gz`  解压指定文件
- `tar -zxvf a.tar.gz -C 解压目录`  解压指定文件到指定目录   目标目录必须存在

---



#### 11.命令别名

- `alias cls='clear'` 起临时别名  下次重连ssh  失效

- ```shell
  # 别名永久生效
  vim ~/.bashrc  #打开配置文件
  alias cls='clear' 
  alias s43='ssh root@43.128.111.99'
  source ~/.bashrc  # 刷新配置文件 使其生效
  
  # 自定义全局别名，在文件末尾追加以下内容
  cp /etc/bash.bashrc /etc/bash.bashrc.backup
  vim /etc/bash.bashrc
  #-----------------------
  # 全局别名设置
  alias cls='clear'
  alias hc='history -c'
  alias s43='ssh root@43.128.111.99'
  alias sfs43='sshfs hxh@43.128.111.99:/home/hxh /mnt/hxh/s43' 
  
  # 查看dns服务器地址
  alias ds='resolvectl dns'
  # 清除dns缓存
  alias dcc='resolvectl flush-caches'
  # 查看dns缓存信息
  alias dcs='resolvectl statistics'
  # 查看所有的tcp,udp连接
  alias nets='netstat -antup'
  
  # 查看所有的服务
  alias servl='netstat -antup'
  # 查看所有启动的服务
  alias servsl='netstat -antup'
  # 启动服务
  alias servo='systemctl start'
  # 关闭服务
  alias servc='systemctl stop'
  # 重启服务
  alias servr='systemctl restart'
  # 查看服务状态
  alias servs='systemctl status'
  
  #-----------------------
  ```



#### 12. awk

```bash
# awk是一种处理文本文件的编程语言，文件的每行数据都被称为记录，默认以空格或制表符为分隔符，每条记录被分成若干字段(列)，awk每次从文件中读取一条记录
# awk [选项] ‘条件{动作}  条件{动作} ... ...’  文件名

free | awk '{print NR}'			#输出行号
free | awk '{print NF}'			#输出每行数据的列数
awk '{print $NF}' /tmp/hosts	#打印每行数据的最后一列
awk '{print $(NF-1)}' /tmp/hosts	#打印每行倒数第二列

cp /etc/hosts /tmp/hosts
awk '{print $0}' /tmp/hosts		#打印每行全部内容

#---------------------------------

# 自定义变量
awk -v x="bob" -v y=10 '{print x,y}' /tmp/hosts

# 调用系统变量
awk -v shell=$SHELL '{print shell}' /tmp/hosts	或者
awk '{print "'$SHELL'"}' /tmp/hosts		#双引号加单引号组合能正确获取系统变量

# 自定义分隔符
awk -F: '{print $1}' /etc/passwd		#以冒号作为分隔符
awk -F"[:,_]" '{print $1}' /etc/passwd	#使用集合定义分隔符

#---------------------------------

awk '/localhost/' /tmp/hosts   # $0~/bash$/   匹配每一行
awk '$3~/local/' /tmp/hosts		#每行的第3列去匹配local
awk '$3~/local/{print $1,$2}' /tmp/hosts
awk '$2=="localhost"' /tmp/hosts		#第2列精确匹配localhost
awk '$2!="localhost"' /tmp/hosts		#取反
awk -F: '$3<=10' /etc/passwd			#第3列小于等于10的行
awk -F: 'NR==10' /etc/passwd			#仅显示第10行
awk -F: '$3>1 && $3<5' /etc/passwd		#逻辑与
awk -F: '$3==1 || $3==5' /etc/passwd	#逻辑或

# BEGIN导致动作指令仅在读取任何数据记录之前执行一次，END导致动作指令仅在读取完所有数据记录后执行一次,BEGIN可以进行数据初始化，END可以进行数据汇总
awk 'BEGIN{print "OK"}'
awk 'END{print NR}' /etc/passwd		#打印最后一行的行号

# 循环计数 逐行读取/etc/passwd文件，x初始值为0，匹配到以bash结尾的行时自加1，最后打印x的值
awk '/bash$/{x++} END{print x}' /etc/passwd

```



#### 13.egrep

```bash
# egrep命令用于在文件内查找指定的字符串

# 查找某个文件是否包含linux字符串
egrep linux /home/a.txt

# 查找当前目录所有包含 linux字符串的文件
egrep linux *
```



#### 14. locate

```bash
# ocate命令用于查找符合条件的文档，他会去保存文档和目录名称的数据库内，查找合乎范本样式条件的文档或目录

# 查找某个文件  支持正则
locate passwd*

# locate 的速度比 find 快，它并不是真的查找，而是查数据库，一般文件数据库在 /var/lib/slocate/slocate.db 中，所以 locate 的查找并不是实时的，而是以数据库的更新为准，一般是系统自己维护，也可以手工升级数据库
updatedb

updatedb & locate wine*
```



#### 15. lsof

```bash
# 查看所有的打开的文件 list open file
lsof

# 查看文件被哪个进程占用
lsof /usr/share/typora/locales/zh-CN.pak

# 查看指定端口对应的进程
lsof -i :80

# 查看指定用户所打开的进程
lsof -u root

# 查看所有的脏数据
lsof | grep deleted
```



#### 16. sed

```bash
<<EOF
sed全称为Stream EDitor，行编辑器，同时也是一种流编辑器，它一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”(pattern space)，接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。然后读入下行，执行下一个循环
EOF

# 查看文件的2到10行内容
sed -n "2,10p" passwd  # cat passwd |sed -n 1p

# 将所有的root(匹配每一行) 替换成alpha  输出内容到屏幕,不修改原文件
sed 's/root/alpha/' passwd

# 将所有的root 替换成alpha 修改文件  # 选项 -i 表示修改文件
sed -i 's/root/alpha/' passwd

# 删除匹配的内容
sed -i '/^test/d' passwd #  sed '/^$/d' file  # 删除空白行  
```



#### 17. grep

```bash
<<EOF
grep命令是Linux系统中最重要的命令之一，功能是从文本文件或管道数据流中筛选匹配的行和数据，如果再配合正则表达式，功能十分强大，是Linux运维人员必备的命令

-v 匹配结果取反
-n 显示行号
-i 忽略大小写
-c 只显示匹配的行数

EOF
#  对结果取反 忽略大小写 只显示匹配的行数
grep -v -i -c 'hxh' passwd
```



#### 18. expect

```bash
# unbuntu 安装expect
# 1. 确保 gcc 和 make 已经安装
sudo apt-get build-dep gcc
apt-get install make

# 2.源码安装 下载tcl和expect
cd /usr/local/src/
wget http://sourceforge.net/projects/tcl/files/Tcl/8.4.19/tcl8.4.19-src.tar.gz
sudo tar -zxvf tcl8.4.19-src.tar.gz
wget http://sourceforge.net/projects/expect/files/Expect/5.45/expect5.45.tar.gz
sudo tar -zxvf expect5.45.tar.gz

# 3.安装 tcl和 expect
cd tcl8.4.19/unix/
sudo ./configure
sudo make
sudo make install
#------------------------------------
cd expect5.45/
sudo ./configure --with-tclinclude=/usr/local/src/tcl8.4.19/generic/ --with-tclconfig=/usr/local/lib/
sudo make
sudo make install

# 4.测试
expect

#-------------------------------------
<<EOF
expect 是由Don Libes基于Tcl（Tool Command Language ）语言开发的，主要应用于自动化交互式操作的场景，借助Expect处理交互的命令，可以将交互过程如：ssh登录，ftp登录等写在一个脚本上，使之自动化完成。尤其适用于需要对多台服务器执行相同操作的环境中，可以大大提高系统管理人员的工作效率

命令：
spawn：交互程序开始后面跟命令或者指定程序（在壳内启动这个进程）
expect：获取匹配信息匹配成功则执行expect后面的程序动作（检测由壳内进程发出的特定交互指令反馈字符串后向下执行）
send：用于向进程发送字符串（从壳外向壳内进程发送一条字符串，换行符为确认结束）
interact：允许用户交互
exp_continue：在expect中多次匹配就需要用到
send_user：用来打印输出 相当于shell中的echo
exit：退出expect脚本
eof：expect执行结束 退出
set：定义变量
puts：输出变量
set timeout：设置超时时间
#-----------------------------------------
EOF


# 实例1 执行之这个脚本 :sh demo
#!/usr/local/bin/expect  # 指定解释器是 expect 而不是bash
spawn ssh root@127.0.0.1  # 启动一个shell进程 发送指令
expect "*password:" # 期望上一条指令的shell结果包含 password:
send "df -h\r" # 发送指令 df -h \r表示回车执行
expect "#" # 期望上一条指令的shell响应结果包含 #
send "exit \r" # 发送退出指令
expect eof  # 退出expect

#-----------------------------------------

#实例2
# 1.如果没有通过spawn开启一个shell进程, 会在shell终端输出 date 而不是具体的日期
# 2.不会用expect解释器取解释date
#!/usr/local/bin/expect
send "date \n"

#-----------------------------------------

#实例3
<<EOF
expect命令和send命令正好相反，expect通常是用来等待一个进程的反馈。expect可以接收一个字符串参数，也可以接收正则表达式参数。比如root登陆的之后，界面会输出一个 #，那么expect此时匹配的是这个#。ssh登陆后，一般shell会返回一个xxxpassword:的输出，那么此时可以匹配password的字符
EOF

#我们改变了send的写法，放在了expect后面使用花挎号夸起来了。这时候，当你执行脚本的时候，你会发现，除非你再键盘上输出hello，然后确认，才会输出“hello world”
#!/usr/local/bin/expect
expect "hello" { send "hello world\n"}

#-----------------------------------------

#实例4
# 不管我们在不在键盘上输入hello，或者输入什么，都会一段时间后，输出【hello world】
# 正常的用法是类似实例3，匹配到指定的字符时，执行指定的动作
#!/usr/local/bin/expect
expect "hello"  
send "hello world\n"

#-----------------------------------------

#实例5
#!/usr/local/bin/expect
set timeout 5
# expect "hello" { send "hello world\n"} # 单个分支，匹配单个情况 类似if
expect {
	"hello" {send "hello world\n"}   # 多个分支,匹配多种情况 类似 if elif
	"hi" {send "hi world"} 
	"bye" {send "bye world"}
}

#-----------------------------------------

# 实例6
#spawn开启一个ssh以后，会进入到一个linux的shell环境下，这时候向进程发送一个hostname这样的字符串，shell就能够识别出这是一个有意义的指令，并返回指令的结果
#!/usr/local/bin/expect
spawn ssh root@127.0.0.1
expect "*password:"

send "111111\r"
expect "#"

send "hostname \r"
expect "#"

send "exit \r"
expect eof

#-----------------------------------------

# 实例7
#有时候，我们可能会发生，停留在界面，等待人工操作的情况。这时候，我们可以用interact指令，来等待人工干预
#!/usr/local/bin/expect
spawn ssh root@127.0.0.1
expect "*password:"

send "111111\r"
expect "#"

send "hostname \r"
expect "#"
interact

```



#### 19. namp

```bash
# 查看局域网内终端连接数量和信息 ip=网络地址+主机号 , 通过子网掩码和ip可以得到主机号
# 使用UDP ping探测主机
nmap -sP 192.168.3.0/24 # 网络地址 192.168.3.0 子网掩码 255.255.255.0 ip地址 192.168.3.10  主机号 10
# 扫描多个端口
nmap -sS 192.168.31.86 -p 20,80,3306,73

# 使用频率最高的扫描选项（SYN扫描，又称为半开放扫描）执行得很快
nmap -PU 192.168.3.0/24　　

# 扫描之后查看arp缓存表获取局域网主机IP地址
nmap -sS 192.168.3.0/24
cat /proc/net/arp
```



#### 20. rsync

> rsync 可以理解为 remote sync（远程同步），但它不仅可以远程同步数据（类似于 scp 命令），还可以本地同步数据（类似于 cp 命令）。不同于 cp 或 scp 的一点是，使用 rsync 命令备份数据时，不会直接覆盖以前的数据（如果数据已经存在），而是先判断已经存在的数据和新数据的差异，只有数据不同时才会把不相同的部分覆盖
>
> ---
>
> 基本命令格式
>
> ```bash
> <<EOF
> 使用 rsync 在远程传输数据（备份数据）前，是需要进行登陆认证的，这个过程需要借助 ssh 协议或者 rsync 协议才能完成。在 rsync 命令中，如果使用单个冒号（:），则默认使用 ssh 协议；反之，如果使用两个冒号（::），则使用 rsync 协议
> ssh 协议和 rsync 协议的区别在于，rsync 协议在使用时需要额外配置，增加了工作量，但优势是更加安全；反之，ssh 协议使用方便，无需进行配置，但有泄漏服务器密码的风险
> EOF
> 
> # SRC：用来表示要备份的目标数据所在的位置（路径）；
> # DEST：用于表示将数据备份到什么位置；
> # USER@：当做远程同步操作时，需指明系统登录的用户名，如果不显示指定，默认为以 root 身份登录系统并完成同步操作
> 
> # 仅在本地备份数据
> rsync [OPTION] SRC DEST
> 
> # 将本地数据备份到远程机器上 : 表示使用 ssh 协议认证
> rsync [OPTION] SRC [USER@]HOST:DEST
> 
> # 将远程机器上的数据备份到本地机器上 : 表示使用 ssh 协议认证
> rsync [OPTION] [USER@]HOST:SRC DEST
> 
> # 将远程机器上的数据备份到本地机器上, :: 表示使用 rsync 协议认证
> rsync [OPTION] [USER@]HOST::SRC DEST
> 
> # 将本地数据备份到远程机器上 : 表示使用 rsync 协议认证 
> rsync [OPTION] SRC [USER@]HOST::DEST
> 
> #---------------------------------------------------
> 
> # 将 /etc/passwd 文件本地同步到本地 /home/hxh/temp目录下，并改名为 a.txt
> rsync -av /etc/passwd /home/hxh/temp/a.txt
> 
> # 将 /etc/passwd 文件本地同步到远程主机 /home/hxh/temp目录下，并改名为 a.txt
> rsync -av /etc/passwd 192.168.3.10:/home/hxh/temp/a.txt
> 
> #----------------------------------------------------
> 
> # 用ssh协议 将远程主机文件夹/home/hxh/ 下所有文件 同步到本地/home/hxh/temp/目录下
> # -avz 递归传输,打印文件信息,传输过程中压缩文件
> rsync -avz root@43.128.111.99:/home/hxh/ /home/hxh/temp/
> 
> # 用ssh协议 将本地主机文件夹/home/hxh/temp 下所有文件 同步到远程/home/hxh目录下
> # --excluden=*.txt 排除 后缀名以txt结尾的文件
> rsync -avz /home/hxh/temp/ --excluden=*.txt root@43.128.111.99:/home/hxh/  # 注意 temp/  而不是 temp
> 
> # 同步源目录和目标目录下所有文件,如果目标目录存在源目录没有的文件,则同步后会目标目录会自动删除它们
> rsync -avz /home/hxh/temp/ --delete  root@43.128.111.99:/home/hxh/
> ```
>
> 常用选项
>
> | OPTION选项        | 功能                                                         |
> | ----------------- | ------------------------------------------------------------ |
> | -a                | 这是归档模式，表示以递归方式传输文件，并保持所有属性，它等同于-r、-l、-p、-t、-g、-o、-D 选项。-a 选项后面可以跟一个 --no-OPTION，表示关闭 -r、-l、-p、-t、-g、-o、-D 中的某一个，比如-a --no-l 等同于 -r、-p、-t、-g、-o、-D 选项。 |
> | -r                | 表示以递归模式处理子目录，它主要是针对目录来说的，如果单独传一个文件不需要加 -r 选项，但是传输目录时必须加。 |
> | -v                | 表示打印一些信息，比如文件列表、文件数量等。                 |
> | -l                | 表示保留软连接。                                             |
> | -L                | 表示像对待常规文件一样处理软连接。如果是 SRC 中有软连接文件，则加上该选项后，将会把软连接指向的目标文件复制到 DEST。 |
> | -p                | 表示保持文件权限。                                           |
> | -o                | 表示保持文件属主信息。                                       |
> | -g                | 表示保持文件属组信息。                                       |
> | -D                | 表示保持设备文件信息。                                       |
> | -t                | 表示保持文件时间信息。                                       |
> | --delete          | 表示删除 DEST 中 SRC 没有的文件。                            |
> | --exclude=PATTERN | 表示指定排除不需要传输的文件，等号后面跟文件名，可以是通配符模式（如 *.txt）。 |
> | --progress        | 表示在同步的过程中可以看到同步的过程状态，比如统计要同步的文件数量、 同步的文件传输速度等。 |
> | -u                | 表示把 DEST 中比 SRC 还新的文件排除掉，不会覆盖。            |
> | -z                | 加上该选项，将会在传输过程中压缩。                           |



#### 21. cat

```bash
# 查看文件,显示行号
cat -n /etc/passwd
# at -A 可以看出隐藏的字符,一般用户多余空格检查,正常情况下每行以 $ 结束
cat -A test.sh 

# 创建文件 ,文件已经存在就是追加内容
# EOF不带单引号表示,shell默认会解析特殊字符，如遇$符号解析变量值
cat >> ~/temp/a.txt << EOF 
hello world
$PATH
echo \$PATH  # 不会解析 PATH , 加 \ 表示转义
xxxxx
EOF

# 不会解析$PATH,直接将原内容追加到 a.txt
cat >> ~/temp/a.txt << 'EOF' 
hello world
$PATH
EOF

# 覆盖原文件内容
cat > ~/temp/b.txt <<'EOF'
qwer
xxx
bbb
EOF

#------------------------------------
# EOF是END Of File的缩写,表示自定义终止符.既然自定义,那么EOF就不是固定的,可以随意设置别名,这个效果跟上面是一样的,表示覆盖原文件内容
cat > ~/temp/b.txt <<'HXH'
qwer
xxx
bbb
HXH
```



#### 22. netstat

```bash
# 查看所有的tcp,udp连接
netstat -antup

# 查询指定进程名信息
netstat -antup |grep openvpn

# 查询进程PID对应的信息
netstat -antup |grep 9057
# udp        0      0 11.0.0.4:1194           0.0.0.0:*                           9057/openvpn

# 查询路由信息
netstat -rn
```



#### 23. ss

> ss 是 Socket Statistics 的首字母缩写。
>
> ss 命令由 iproute2 软件包提供（`yum install iproute`）。
>
> iproute 2 包中的命令可以完全替代 net-tools 包中的 ifconfig、netstat、route 等命令。
>
> ##### 无选项
>
> 单独使用的 ss 命令用于显示**已建立**的网络连接。例如：
>
> ```text
> # ss
> Netid  State  Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
> tcp    ESTAB  0       0       10.0.2.10:ssh       10.0.2.2:52316
> ```
>
> 上述输出结果中的 ESTAB 代表网络连接的状态是“已建立”（Established）。
>
> ###### -h 选项
>
> -h 或 --help 用于显示帮助信息。
>
> 当然，也可以使用 `man ss` 命令查看更详细的帮助文档。
>
> ###### -a 选项
>
> -a 或 --all 用于显示所有的网络连接：监听中和非监听中（对于 tcp，就是监听中和已建立）。例如：
>
> ```text
> # ss -a
> Netid  State  Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
> tcp   ESTAB   0       0       10.0.2.10:ssh       10.0.2.2:52316
> tcp   LISTEN  0       128     [::]:ssh            [::]:*
> ```
>
> ###### -l 选项
>
> -l 或 --listening 用于显示处于**监听**状态的网络连接。例如：
>
> ```text
> # ss -l
> Netid  State  Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
> tcp    LISTEN 0       128     *:ssh               *:*
> ```
>
> 上述输出结果中的 LISTEN 代表网络连接的状态是“监听中”（Listening）。
>
> ###### -n 选项
>
> -n 或 --numeric 用于显示端口号，而非使用该端口号的服务。例如：
>
> ```text
> # ss -n
> Netid  State  Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
> tcp    ESTAB  0       0       10.0.2.10:22        10.0.2.2:52316
> ```
>
> 使用选项 -n 后，显示端口号 22，而非使用 22 端口号的 ssh。
>
> ###### -p 选项
>
> -p 或 --processes，显示端口对应的进程名和进程号 PID。
>
> ###### 其它常用选项
>
> -t 或 --tcp，仅显示 tcp 端口。
>
> -u 或 --udp，仅显示 udp 端口。
>
> -x 或 --unix，仅显示 Unix domain sockets。





#### 24. iptables

> Iptables 是用来设置、维护和检查Linux内核的IP包过滤规则的
>
> 可以定义不同的表，每个表都包含几个内部的链，也能包含用户定义的链。每个链都是一个规则列表，对对应的包进行匹配：每条规则指定应当如何处理与之相匹配的包。这被称作'target'（目标），也可以跳向同一个表内的用户定义的链
>
> 防火墙的规则指定所检查包的特征，和目标。如果包不匹配，将送往该链中下一条规则检查；如果匹配，那么下一条规则由目标值确定.该目标值可以是用户定义的链名，或是某个专用值，如ACCEPT[通过],DROP[删除],QUEUE[排队]，或者 RETURN[返回]
>
> > ACCEPT 表示让这个包通过
> >
> > DROP表示将这个包丢弃
> >
> > QUEUE表示把这个包传递到用户空间
> >
> > RETURN表示停止这条链的匹配，到前一个链的规则重新开始。如果到达了一个内建的链（的末端），或者遇到内建链的规则是RETURN，包的命运将由链准则指定的目标决定
>
> ---
>
> tables :  (4表5链)   raw ,mangle , nat ,filte       PREROUTING ,POSTROUTING,  INPUT, FORWARD ,OUTPUT
>
> > filter，这是默认的表，包含了内建的链INPUT（处理进入的包）、FORWARD（处理通过的包）和OUTPUT（处理本地生成的包）。
> >
> > nat，这个表被查询时表示遇到了产生新的连接的包，由三个内建的链构成：PREROUTING （修改到来的包）、OUTPUT（修改路由之前本地的包）、POSTROUTING（修改准备出去的包）。
> >
> > mangle 这个表用来对指定的包进行修改。它有两个内建规则：PREROUTING（修改路由之前进入的包）和OUTPUT（修改路由之前本地的包）
> >
> > tables应用顺序  raw >> mangle >> nat >>filter ,如果两个表同时某个规则起作用, 按照表顺序处理, 如果前面的表已经丢包, 后面的表则接受不到该包
> >
> > 链顺序 intput >> forward >> output
> >
> > 规则顺序 逐行匹配
>
> ---
>
> 语法：
>
> `iptables  -t 表名  管理选项 [链名] [匹配条件] [-j 规则]`
>
> -t 指定表名 默认不写 则是filter表
>
> 管理选项 增加A、删除D、查看L
>
> 链名  PREROUTING ,POSTROUTING,  INPUT, FORWARD ,OUTPUT
>
> 匹配条件 ： 数据包特征：ip、端口
>
> -j   ACCEPT,  DROP,  REJECT,  LOG
>
> ---
>
> 如: 拒绝所有的ICMP协议的网络请求 ,链名，动作名必须大写 INPUT，REJECT
>
> `iptables -t filter -A INPUT -p icmp -j REJECT `   -A 在行位加入规则  -I 在行首加入规则
>
> `iptables -t filter -D INPUT -p icmp -j REJECT`   删除拒绝所有的ICMP协议的网络请求的规则
>
> Chain INPUT(链名)    target(策略)  prot(协议) opt(选项)  source(源地址) destination(目标地址)
>
> iptables -L|head -n 10   
> Chain INPUT (policy ACCEPT)
> target     prot opt source               destination         
> REJECT     icmp --  anywhere             anywhere             reject-with icmp-port-unreachable
> ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ddi-tcp-1
> ACCEPT     udp  --  anywhere             anywhere             udp dpt:openvpn
> f2b-SSH    tcp  --  anywhere             anywhere             tcp dpt:ssh
>
> ---
>
> 操作方法:
>
> `iptables -t filter -A INPUT -p 80 -j REJECT`   拒绝80端口数据包
>
> `iptables -L`  查看所有的规则
>
> `iptables -n`   显示数字
>
> `iptables -v`  显示详细信息
>
> `iptables -L --line-numbers`  显示行号
>
> -F 清空规则   -X  清理自定义的链   -Z 清理规则序号
>
> `iptables -t filter INPUT -P DROP`   修改 filter表中的 INPUT链 默认策略是 丢弃
>
> ---
>
> 详细匹配规则:
>
> 通用匹配:  -p   可以匹配协议、地址、端口
>
> ```bash
> iptables -t filter -I INPUT -p icmp -j REJECT  # 拒绝这个icmp协议的请求
> iptables -t filter -I INPUT -p udp --dport 1194 -j DROP  # 拒绝 udp协议访问本机1194端口的请求
> 
> iptables -t filter -I INPUT -s 192.168.3.12 -j REJECT # 拒绝这个地址网络请求
> iptables -t filter -I INPUT -i enp3s0 -j REJECT  # 拒绝这个网卡enp3s0接口的所有请求
> 
> # ------------------------------------
> # 设置本机可以ping 别的主机，而其他主机不能ping本机
> iptables -t filter -I INPUT -p icmp --icmp-type 8 -j REJECT
> 
> # 删除某条规则流程
> iptables -t filter -L -n --line-numbers # 显示行号的方式查看filter表中的规则
> iptables -t filter -D INPUT 1 # 删除filter表中 INPUT链的第一行规则
> ```
>



#### 25. tcodump

```bash
# 基于协议过滤---------------------
# 抓取所有数据包
tcpdump

# 指定抓取来自 192.168.10.100 的包
tcpdump host 192.168.10.100

# 指定抓取数据源[数据目标]地址是 192.168.10.100
tcpdump src[dst] 192.168.10.100

# 过滤某个网段地址
tcpdump net 192.168.10.0/24


# 基于端口过滤---------------------
# 抓取tcp协议80端口的报文
tcpdump tcp port 80

# 抓取数据包 源端口是80或者22的数据包
tcpdump src port 80 or 22

# 抓取数据包 源端口是80到8080的数据包
tcpdump src portrange 80-8080


# 基于协议过滤---------------------
tcpdump icmp[ip/udp/arp]  # 传输层可以直接写协议名

tcpdump port http[/https/ftp/ssh/dns] # 应用层必须加上port


# 基于常用参数过滤---------------------
# 抓取发往ens33网卡的icmp协议数据报文
tcpdump -i ens33 icmp

# 抓取的数据包文保存到 test.pcap文件中,可以用第三方抓包软件[Wireshark]打开此文件
tcpdump -w test.pcap
# 从test.pcap文件读取数据
tcpdump -r test.pcap

# -n  不讲ip转成域名,避免dns查询,提高速度
# -nn 不把协议和端口号转成名字,提高抓包速度
# -c 20  抓取20次报文后退出
# -Q in  | -Q out   抓取进来|出去的数据包  tcpdump -Q in -i en0 src 192.168.10.150
# -q 简洁的数据信息 tcpdump -q -i en0 src 192.168.10.150
```



#### 26. nslookup

```bash
# 查询本地域名服务器配置文件
cat /etc/resolv.conf

# 查看域名的域名对应的ip信息
nslookup baidu.com
#--------------------
Server:		223.5.5.5
Address:	223.5.5.5#53

Non-authoritative answer:
Name:	baidu.com
Address: 39.156.66.10
Name:	baidu.com
Address: 110.242.68.66
#--------------------

# 使用域名指定的dns服务器解析,域名权威的dns服务器可以通过 https://whois.aliyun.com/ 查出来
nslookup baidu.com NS1.BAIDU.COM  
#--------------------
Server:		NS1.BAIDU.COM
Address:	110.242.68.134#53

Name:	baidu.com
Address: 39.156.66.10
Name:	baidu.com
Address: 110.242.68.66
#--------------------

# 查询域名txt记录信息
nslookup -q=txt bilibili.com
```



#### 27. traceroute

```bash
# 跟踪路由,默认以udp协议跟踪
traceroute www.bilibili.com

# 以icmp协议方式跟踪路由
traceroute -I www.bilibili.com 

# 测试 google.com
traceroute -I www.google.com
#--------------------
traceroute to www.google.com.hk (69.63.186.31), 64 hops max, 72 byte packets
 1  192.168.10.1 (192.168.10.1)  2.494 ms  2.740 ms  2.399 ms
 2  192.168.1.1 (192.168.1.1)  2.596 ms  2.460 ms  3.127 ms
 3  172.18.0.1 (172.18.0.1)  7.128 ms  5.362 ms  4.788 ms
 4  183.233.23.69 (183.233.23.69)  12.411 ms  8.061 ms  5.853 ms
 5  * * *
 6  * * 221.183.39.173 (221.183.39.173)  15.098 ms
 7  221.183.89.245 (221.183.89.245)  14.631 ms * *
 8  * * *
 9  * * *
10  * * *
11  * * *
#--------------------
# google.com实际ip地址已经查询到(69.63.186.31),但是在访问 221.183.89.245 ip地址,后面一直没有数据返回,则表示这个路由器和谐掉了 谷歌的真实ip
```



#### 28. telnet && nmap

```bash
# 查看服务器的端口是否打开,可以遍历命令这个主机,查看这个服务器开启那些端口
telnet www.baidu.com 80

# nmap 扫描这个网段哪些主机在线
nmap -sP 192.168.10.1/24

# nmap 扫描这个主机哪些端口开放
nmap 192.168.10.100
```



#### 29. sudo

```bash
# 提升普通用户的权限 权限文件所在的位置:/etc/sudoers  通过 自动的命令 visudo 来修改用户的权限
useradd test1
visudo
#----------
#............
## Allow root to run any commands anywhere
root    ALL=(ALL)   ALL
test1   ALL=(ALL)   ALL
#............
#----------
su test1
sudo cat /etc/shadow  #   输入test1 的密码 就可以行使 root权限

  
# 将用户加到 wheel这个组 也可用通过sudo执行 root权限的指令
useradd test2
usermod -aG test2 wheel


# 对普通用户权限限定
visudo
#-------------
## Allow root to run any commands anywhere
root    ALL=(ALL)   ALL
# 只允许 test1用户在任意主机任意ip |ALL=(ALL) 执行 useradd  权限的root命令
test1   ALL=(ALL)   /usr/sbin/useradd
#-------------

# 查看普通用户可以执行哪些root命令
sudo -l
```





### 配置文件

#### 类别区分

```bash
# 全局配置
/etc/profile
/etc/profile.d/*.sh
/etc/bashrc

# 个人配置
~/.bash_profile
~/.bashrc
```



#### 登录方式区分

##### 交互式登录

- 直接通过终端输入账号密码
- 终端使用 su - userName 切换用户

```bash
# 以上两种登录方式会自动以下列顺序触发脚本
/etc/profile -> /etc/profile.d/*.sh -> ~/.bash_profile -> ~/.bashrc -> /etc/bashrc
```

##### 非交互式登录

- su userName
- 图形界面打开终端
- 执行脚本
- 任何其他bash实例

```bash
/etc/profile.d/*.sh -> /etc/bashrc -> ~/.bashrc
```



#### 功能区分

##### profile

> profile类为交互式登录提供shell配置
>
> 全局: /etc/profile  /etc/profile.d/*.sh
>
> 个人: ~/.bash_profile
>
> 功能:用于定义环境变量,运行命令,以及脚本

##### bahsrc

>bashrc为非交互式登录提供shell配置
>
>全局: /etc/bashrc
>
>个人: ~/.bashrc
>
>功能: 定义别名和函数,定义本地变量



#### 附加

~/.bash_logout 退出执行的脚本



### 组、权限管理

#### 1.组

- `chown user 文件名`  修改文件的拥有者   chown root a.txt
- `chown -Rf ftp.ftp pub`  强制修改目录下所有文件是ftp组的ftp成员, pub为目录
- `chown -Rf ftp pub` 强制修改目录下所有文件是 ftp用户
- `groupadd 组名` 创建组
- `chgrp 组名 文件名` 修改文件所在组
- `usermod -g root alpha`  改变用户所在组
- `usermod -d /home/home alpha` 修改用户初始登录进入的目录

#### 2.权限

> 文件类型：
>
> `drwx------ 3 admin admin   4096 Aug  7 20:06 admin`
>
> `-rw-r--r-- 1 alpha dev  12 Aug 14 07:20 a.txt`
>
> \-  文件   | d 目录   |   c 设备文件,鼠标  |  l 软链接   |  b 块文件,硬盘
>
> r 读 w 写 x 执行  - 没得权限    rw-r--r--  依次表示  文件拥有者权限  组权限 和其他组权限
>
> 1 如果是文件  表示硬链接    如果是目录  1表示子目录个数
>
> alpha dev  文件拥有者  文件所在组
>
> 4096  4096表示目录    12 表示文件大小 单位 字节
>
> Aug 14 07:20  文件最后修改时间
>
> a.txt  文件名

> rwx作用到文件上
>
> r 代表对文件读
>
> w 可以修改 但不一定能删除  删除的前提是该用户拥有对该目录拥有写权限
>
> x 代表该文件可被执行
>
> ---
>
> rwx作用到目录上
>
> r 对目录内容进行查看操作
>
> w 修改目录名 对该目录 创建 删除 等
>
> x 代表可进入该目录

- r 4  |  w 2  |  x 1
- `chmod 764 a.txt`  修改文件权限  7(4+2+1)(文件拥有者权限)  6(4+2)(文件所在组权限) 4 (4)(文件其他组权限)
- `chmod u-x a.txt` 给文件拥有者减去执行权限
- `chmod g+w a.txt` 给文件所在组加上可写权限
- `chmod o+r a.txt` 给文件其他组加上可读权限
- `chmod a-x a.txt` 给所有人减去可执行文件
- `chown -R alpha test` 将test目录下所有的文件包括目录所有者修改成alpha
- `chgrp -R dev test` 将test目录下所有的文件包括目录下的组修改成dev
- `chmod -R 777 test` 将test目录下所有的文件包括目录 权限修改成777

---



### 任务

#### 1.crond 任务调度

- `crontab -e` 编辑定时任务
- `crontab -l` 查看定时任务列表
- `crontab -r`终止定时任务
- `service crond restart` 重启任务调度

#### 2.cron表达式解析

> minute： 表示分钟，可以是从0到59之间的任何整数。
>
> hour：表示小时，可以是从0到23之间的任何整数。
>
> day：表示日期，可以是从1到31之间的任何整数。
>
> month：表示月份，可以是从1到12之间的任何整数。
>
> week：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日

> 星号（*）：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。
>
> 逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”
>
> 中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”
>
> 正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。
>
> ---
>
> 实例1：每1分钟执行一次command
>
> ​    * /1  * * * *   
>
> 实例2：每小时的第3和第15分钟执行
>
> 3,15 * * * * command
>
> 实例3：在上午8点到11点的第3和第15分钟执行
>
> 3,15 8-11 * * * command
>
> 实例4：每隔两天的上午8点到11点的第3和第15分钟执行
>
> 3,15 8-11 */2 * * command
>
> 实例5：每个星期一的上午8点到11点的第3和第15分钟执行
>
> 3,15 8-11 * * 1 command
>
> 实例6：每晚的21:30重启smb 
>
> 30 21 * * * /etc/init.d/smb restart
>
>
> 实例7：每月1、10、22日的4 : 45重启smb 
>
> 45 4 1,10,22 * * /etc/init.d/smb restart

#### 3.示例

```linux
# 示例
crontab -e # 编写简单定时任务
# 每个1分钟 将 date内容追加到 /home/test/a.txt文件中
# 创建taskdemo.sh 然后给与执行权限
touch /home/test/taskdemo.sh
chmod u+x taskdemo.sh

*/1 * * * * /home/test/taskdemo.sh

#taskdemo.sh
date >> /home/test/a.txt

# 查看内容
tail -f /home/test/a.txt
```

```linux
yum install crontabs

/sbin/service crond start  #启动服务
/sbin/service crond stop  #关闭服务
/sbin/service crond restart  #重启服务
/sbin/service crond reload  #重新载入配置
/sbin/service crond status  #启动服务
```



---







### 进程、服务

#### 1.`ps aux`  查看进程信息

| USER | PID    | %CPU      | %MEM       | VSZ            | RSS            | TTY        | STAT       | START        | TIME              | COMMAND    |
| ---- | ------ | --------- | ---------- | -------------- | -------------- | ---------- | ---------- | ------------ | ----------------- | ---------- |
| root | 2      | 0.2       | 0.2        | 43584          | 3964           | ?          | Ss         | Aug13        | 0:02              | [kthreadd] |
| 用户 | 进程ID | 占用的CPU | 占用的内存 | 占用的虚拟内存 | 占用的物理内存 | 使用的终端 | 进程的状态 | 进程开始时间 | 进程占用cpu的时间 | 运行的命令 |

#### 2.`ps -ef`  查看父进程 PPID

- `kill PID` 结束进程

- `killall 进程名称`  结束进程 支持通配符

- `kill -9 PID` 强制结束指定进程

#### 3.`pstree -pu`  进程树

#### 4.示例

```bash
# 踢出某登录用户
ps aux|grep sshd
#root      1383  0.0  0.2 112940  4344 ?        Ss   Aug13   0:00 /usr/sbin/sshd -D
#root     19253  0.0  0.3 152668  5396 ?        Ss   04:52   0:00 sshd: root@pts/0
#root     19821  0.0  0.3 152668  5396 ?        Ss   06:21   0:00 sshd: root@pts/1

kill 19821

# 终止远程登录sshd服务
kill 1383

#终止服务名以 v2ray开头的服务
killall v2ray*
```

---

#### 5.服务

> 服务本质就是进程，但是在后台运行，通常会监听某个端口，如ssh 22端口  mysql 3306端口...  等待其他程序的连接请求，因此我们又可以称之为守护进程
>
> ---
>
> service 服务名  start|stop|status|restart|reload
>
> centOS7之后  使用systemctrl
>
> ---
>
> 检测linux某个端口是否开放  windows命令行
>
> `telnet ip port`  telnet 47.242.52.171 8080
>
> ---
>
> `systemctl start|stop firewalld.service`  开启|关闭防火墙
>
> `firewall-cmd --list-ports`  查看开启的端口
>
> `firewall-cmd --zone=public --add-port=80/tcp --permanent` 开启80端口

- `systemctl list-unit-files`  查看所有服务
- `systemctl  list-units --type=service`  显示所有启动的服务
- `systemctl start|stop|restart|status  httpd.service`  启动|停止|重启某个服务
- `systemctl enable|disable v2ray.service` 设置服务开启自启 | 不自启服务

#### 6.top

>top - 07:12:55 up 1 day, 21:15,  1 user,  load average: 0.00, 0.02, 0.05
>Tasks:  83 total,   1 running,  82 sleeping,   0 stopped,   0 zombie
>%Cpu(s):  0.3 us,  0.2 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
>KiB Mem :  1776176 total,   355944 free,   216020 used,  1204212 buff/cache
>KiB Swap:        0 total,        0 free,        0 used.  1364420 avail Mem 
>
>---
>
>07:12:55 当前时间
>
>up 1 day, 21:15  系统已经运行时间
>
>1 user  当前1个用户登陆
>
>0.00, 0.02, 0.05  负载均衡    三个值加起来除以3  结果超过0.7 说明系统带不动了
>
>Tasks:  83 total,   1 running,  82 sleeping,   0 stopped,   0 zombie  83个任务  1个在运行  .....   zombie  僵尸进程
>
>%Cpu(s):  0.3 us,  0.2 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st   CPU信息
>
>KiB Mem :  1776176 total,   355836 free,   215948 used,  1204392 buff/cache   内存信息
>
>KiB Swap:        0 total,        0 free,        0 used.  1364492 avail Mem   虚拟内存信息

- `top -p pid`  监控某个具体进程
- `top -i`  不显示任何闲置或者僵尸的进程
- `top -d 1` 1s更新一次进程信息

> top 进入交互状态   
>
> u  选择监控的用户
>
> k 选择杀死的进程pid
>
> P 按cpu占用率 排序
>
> M 按内存使用率排序
>
> q  退出交互界面

#### 7.netstat 

- `netstat -anp` 查看所有网络服务进程
- `netstat -anp |grep sshd` 查看某个网络服务的信息
- `netstat -antulp | awk '{print $4}' | grep ":22"`  查看 22 端口是否开启

> **IP**
>
> ipv4:地址总共有32位。ip地址的表示方式有2种，一种是二进制，一种是十进制。
>
> 十进制：192.168.1.1。
>
> 二进制：根据我的电脑的ip地址192.168.1.1，换算二进制是：11000000.10101000.00000001.00000001（最后1位，是2的0次方就是1，依次往后计算），如果涉及子网划分，需要知道二进制是如何换算十进制的。
>
> 无论十进制还是二进制，以.进行区分，分为4部分。
>
> 工作中常用的是十进制
>
> ---
>
> ip地址分成4段，并用.分隔。例如：192.168.0.1
>
> 结构：ip地址=网络号+主机号
>
> 可能 192.168 是网络号，后面的 .0.1 是主机号。也有可能 192.168.0这3段都是网络号，后面的 .1 是主机号
>
> ---
>
> 网络号是用来标识一个子网的。什么是子网？
>
> 网络层确定主机是不是在同一个子网，只有在同一个子网的主机才能直接通信。因为在同一个网段才能直接通信。
>
> 网络号标识两台机器是不是在同一个子网。网络号相同，表示是同一个子网，它们两个就能直接通信。
>
> 主机号标识的是子网中的某台主机。
>
> 比如主机号是个1，表示具体的主机，例如我的电脑的ip地址是192.168.1.4，那么.4可能是我的主机号。
>
> 光看192.168.1.4这样一个iPv4地址是没有办法判断网络号和主机号的。因为没有确定子网掩码
>
> ---
>
> iPv4子网掩码作用：用来标识子网，必须跟ip地址一起存在。
>
> 如果别人说我的ip地址是192.168.1.4是错误的，必须跟子网掩码一起存在，例如192.168.0.1 255.255.255.0，这样是个完整的ip地址。
>
> 组成：子网掩码跟ip地址一样，二进制:由连续的1和0组成，连续的1表示的是网络地址，连续的0表示的是主机地址。
>
> 只有网络地址相同的主机在同一个子网，才能直接通信。
>
> 表示方式：255.255.255.0，换算成二进制就是这样：11111111.11111111.11111111.00000000
>
> 255.255.255表示的是网络位置，后面的0表示的是主机位置。
>
> 192.168.0.1 255.255.255.0中192.168.0表示的都是网络号，ip地址中的1对应子网掩码中的0，是主机号。
>
> 这样才能完整的标识出一个ip地址，判断出它的网络号和主机号
>
> ---
>
> 例1:
>
> 机器1: 192.168.1.87 255.255.255.0
>
> 机器2: 192.168.2.77 255.255.255.0
>
> 255.255.255.0是子网掩码。
>
> 如何判断它们能直接通信吗？先判断它们是否在同一个子网。
>
> 机器1的主机地址对应的子网掩码是：.0 机器2的主机地址对应的子网掩码是：.0
>
> 机器1的网络地址对应的子网掩码是：255.255.255 机器2的网络地址对应的子网掩码是：255.255.255
>
> 机器1的主机地址：.87 机器2的主机地址：.77
>
> 机器1的网络地址是：192.168.1 机器2的网络地址是：192.168.2
>
> 它们的网络地址不一样，所以它们不在一个子网，不能直接通信
>
> ---
>
> 例2:
>
> 如果把机器1的子网掩码改成：255.255.0.0
>
> 机器2的子网掩码改成:255.255.0.0
>
> 子网掩码255.255对应的192.168是网络号。
>
> 机器1的主机地址：.1.87 机器2的主机地址：.2.77
>
> 所以，机器1的网络地址是：192.168 机器2的网络地址是：192.168
>
> 它们两个现在的网络地址是一样的，能直接通信的

---





### 包管理

#### 1.rpm

> [root@izj6c1n90e758nubb3uipvz ~]# rpm -qa|grep tar
> tar-1.26-35.el7.x86_64
> selinux-policy-targeted-3.13.1-268.el7_9.2.noarch
>
> el7  适用centOS7
>
> 86_64  该软件适合运行的环境
>
> noarch  该软件是通用的

- `rmp -qa |grep docker`  查看docker软件包是否安装
- `rpm -qi docker` 查看软件包的安装信息
- `rpm -ql docker` 查看软件包安装的位置
- `rpm -qf /etc/passwd`  查看文件属于哪个文件包
- `rpm -e docker` 删除docker软件包
- `rpm -e --nodeps docker`  强制删除软件包(不建议)
- `rpm -ivh 软件包全路径` 安装 rpm包  i 安装 v提示 h 进度条

#### 2.yum

- `yum install 软件包` 安装软件包 自动处理依赖关系
- `yum list |grep docker` 查看软件包是否存在

---



### 磁盘管理

#### 1.新增虚拟内存(磁盘分区)

```bash
# 查看所有磁盘的信息,-f 显示分区文件系统
lsblk -f

# 查看磁盘各个分区的详细信息
fdisk -l

#-----------------------
# 以第二块磁盘分区的方式增加虚拟内存,原生是不支持分区扩容的,第三方支持原磁盘虚拟内存扩容,但是不安全
# 查看虚拟内存
free -m

# 1.对一块新的磁盘分区,进入交互模式
fdisk /dev/sdb

# 2. 输入 m 查看帮助,输入 n 新建分区
n

# 3.上一步结束后会提示创建分区类型,输入p 创建主分区
# mbr分区表对于一块磁盘来说,支持3个主分区,1个扩展分区, 主分区命名格式为 sdb1,sdb2,sdb3,sd5~sdb16(扩展分区是概念上的,一个扩展分区支持12逻辑分区)
p

# 4.输入分区id 1,默认是1
1

# 5.输入起始扇区,默认2048,默认即可
2048

# 6.输入结束扇区的大小,这一步其实是规定这个分区大小,我们可以输入 +2G , +200M, 确定其大小
+2G

# 7.输入 t,修改分区的id
t

# 8.确定分区的id是82,为交换分区
82

# 9. 输入w保存退出
w

# 10.查看修改后的信息 ,如果是进行磁盘分区不是swap,要使用自定义的分区,则需要对分区进行格式化,也就是指定其文件系统
# mkfs.xfs /dev/sdb2  则是将 /dev/sdb2这个分区设置成xfs文件系统
fdisk -l /dev/sdb1
磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x8c9422c7

   设备 Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     4196351     2097152   82  Linux swap / Solaris

# 11.持久挂载该交换分区,新增swap分区的UUID 可以通过 lsblk -f 查询
cat >>/etc/fstab<<EOF
UUID=25380378-4d98-4675-bfd6-06c7582346a8   swap    swap defaults       0 0
EOF

# 12.使配置生效,swap -s 查看有哪些 swap分区
swapon
```



#### 2.新增虚拟内存(swap文件)

```bash
# 以文件的方式增加虚拟内存性能不如磁盘分区的方式提供虚拟内存, windows是以文件方式增加虚拟内存的,叫做分页文件

#-----------以文件方式增加虚拟内存
# 1.创建 1G 大小的文件
root dd if=/dev/zero of=/swapfile bs=1M count=1024

# 2.将文件的格式设置成虚拟内存 
mkswap /swapfile

# 3.配置虚拟内存文件
cat >> etc/fstab << EOF
/root/swapfile          swap                    swap    defaults        0 0
EOF

# 4.修改文件权限 并挂载虚拟内存文件
chmod 600 /swapfile && swapon -a

# 禁用swap分区文件
swapoff /swapfile
```



#### 3. ISO

```bash
# 1.将光盘内容制作ISO文件
cp /dev/sr0 /centos7.ios

# 2.将文件夹制作成iso文件,将 /etc这个文件夹制作成ios文件 ,存放的位置是 /root/etc.iso
# yum -y install genisoimage
mkisofs -r -o /root/etc.iso /etc
```



#### 4. USB

```bash
# linux 不支持 ntfs文件系统, fat文件格式支持 linux 和 windows
yum install -y dosfstools.x86_64
mkfs.fat /dev/sdd1
mount /dev/sdd1 /mnt/media

# 卸载usb设备, sync 是确保 缓冲区的文件存到 usb移动存储设备中
sync && umount /mnt/media
```



#### 5. 工具

```bash
# 查看分区对应的信息(容量,文件系统,挂载点...)
lsblk -f
df -h

# 显示目录大小,默认显示当前文件夹
# 查看/这个目录下每个文件夹总占用空间
du -sh /*

# 创建文件
# if(input file)从哪个文件读数据, /dev/zero 生产随机数据
# of(output file)输入数据到哪个文件
# bs 一次读取多大的数据
# count 生成多少次数据
# 最终这个文件的大小是 1024M
dd if=/dev/zero of=./a.iso  bs=1M count=1024

# 对整个分区进行克隆(1:1完全复制)
dd if=/dev/sda1 of=centos_bakcup.img
```



#### 6.LVM

> LVM: Logical Volume Manager 可以允许对卷进行方便操作的抽象层，包括重新设定文件系统的大小，允许在多个物理设备间重新组织文件系统
>
> 通过交换PE来进行资料的转换，将原来LV内的PE转移到其他的设备中以降低LV的容量，或将其他设备中的PE加到LV中以加大容量 

##### 分区创建逻辑卷

```bash
# 安装相关工具包
yum install -y lvm2

# 创建物理卷, /dev/sdb1 分区创建    /dev/sdc 整块磁盘创建物理卷
pvcreate /dev/sdb1 /dev/sdc
# 查看创建的物理卷
pvs
# 查看物理卷详情
pvdisplay

#-----------------

# 将上一步创建的物理卷加入卷组, -s 可以指定PE大小,默认值是4MB ,vg_mysql是卷组的名字
vgcreate vg_mysql /dev/sdc /dev/sdb1
# 查看卷组,卷组大小是同一组物理卷大小的总和
vgs
# 查看卷组详情
vgdisplay

#-----------------

# 创建逻辑卷,-n指定逻辑卷的名字 -L 指定逻辑卷的大小
# 从 vg_mysql这个卷组中划分500M(-L 500M)的大小 创建一块名为 lv_data(-n lv_data)的逻辑卷
lvcreate -n lv_data -L 500M vg_mysql

# 查看逻辑卷
lvs

# 查看逻辑卷详情
lvdisplay

# 生成逻辑卷的软连接
ll /dev/vg_mysql/lv_data
# lrwxrwxrwx 1 root root 7 3月  13 03:08 /dev/vg_mysql/lv_data -> ../dm-2

#-----------------

# 给逻辑卷生成文件系统
mkfs.xfs /dev/vg_mysql/lv_data

# 将逻辑卷配置到文件中,永久生效,自动挂载
# UUID ,文件系统类型可以通过  lsblk -f 或者 blkid 查看
vim /etc/fstab
#------------------------
#.....
UUID=S2c11b-OJDa-POQH-P9eu-H4PK-jFki-6pSM9P     /mysql/data     xfs     defaults        0 0
#------------------------

# 挂载设备
mount -a


#------------------------

# 给逻辑卷扩容
# df 查看逻辑卷位置
lvextend -L +1G /dev/mapper/vg_mysql-lv_data

# 同步文件系统, ext4文件系统同步命令是 resize2fs
xfs_growfs /dev/vg_mysql/lv_data

# 将创建的逻辑卷加入到已存在的卷组中  
vgextend vg_mysql /dev/sdb2

# 逻辑卷扩容
# -r 自动识别逻辑卷的文件系统,不需要根据命令指定文件系统对应的命令进行扩容  
lvextend -r -L +2G /dev/mapper/vg_mysql-lv_data
```





### vim 

> 1.打开文件并指定行数   `vim helloc +10`
>
> 2.定位到末行 `vim hello.c + `
>
> 3.异常退出导致文件打开错误  可以删除交换文件  `d`
>
> > ```shell
> > While opening file "hello.c"
> >              dated: Mon Apr 11 14:12:14 2022
> > 
> > (1) Another program may be editing the same file.  If this is the case,
> >     be careful not to end up with two different instances of the same
> >     file when making changes.  Quit, or continue with caution.
> > (2) An edit session for this file crashed.
> >     If this is the case, use ":recover" or "vim -r hello.c"
> >     to recover the changes (see ":help recovery").
> >     If you did this already, delete the swap file ".hello.c.swp"
> >     to avoid this message.
> > 
> > Swap file ".hello.c.swp" already exists!
> > [O]pen Read-Only, (E)dit anyway, (R)ecover, (D)elete it, (Q)uit, (A)bort:
> > ```
>
> 

#### vim三种模式

##### 命令模式

> 重复命令  
>
> > 输入一个数字n,  然后跟上命令既可以使命令重复执行n次 如：  
> >
> > `5 k` 向上移动5行
> >
> > `10 yyp` 将当前行复制10次
>
> ----
>
> 
>
> 移动和选择
>
> > h (食指)   j （食指） k（中指） l（无名指）  左下上右
> >
> > 行内移动
> >
> > - w 向后移动一个单词位置
> > - b  向前移动一个单词位置
> > - 0 定位到行首
> > - ^ 定位到行首不是空白字符位置
> > - $ 定位到行尾
>
> > ----
>
> 
>
> 行数移动
>
> > - gg  文件顶部
> > - G 文件末尾
> > - 10gg  移动到第10行
> > - 10G  移动到第10行
> > - : 10  一定到第10行
> >
> > -----
>
> 
>
> 屏幕移动
>
> > ctrl+b  向上移动屏幕
> >
> > ctrl+f   向下移动屏幕
> >
> > H     移动到屏幕顶部
> >
> > M    移动到屏幕中间
> >
> > L      移动到屏幕底部
> >
> > {     向上移动一个段落
> >
> > }     向下移动一个段落
> >
> > % 匹配一行中的 ( )  { }  [ ]
>
> 标记
>
> mx  (a-z,A-Z任意一个字母设置标记)
>
> 'x   回到标记处，如果后面设置相同字母的标记 ,则之前的标记会被覆盖，标记行被删除 ，标记也会被删除
>
> ----
>
> 
>
> 选择文本
>
> >v   从光标位置开始选择文本
> >
> >V   选中光标移动的所在行
> >
> >ctrl+v  垂直方向选中文本
>
> ---
>
> 

> 编辑操作（删除、复制、替换、粘贴、缩排）
>
> > `set nu`  显示行号
>
> ---
>
> 
>
> 撤销
>
> > u   撤销上一步指令
> >
> > ctrl+r   恢复撤销命令
>
> ---
>
> 
>
> 删除
>
> > x    删除光标所在字符  或者选中文字
> >
> > d   删除移动命令对应的内容
> >
> > > dw  删除至单词末尾
> > >
> > > d0  从光标位置删除到一行起始位置
> > >
> > > d}   删除至当前段落
> > >
> > > ndd  从光标当前位置往下删除n行
> > >
> > > d'x    删除至标记位置
> >
> > dd  删除当前行,   3dd删除3行
> >
> > D  删除至行尾
>
> ---
>
> 
>
> 复制和粘贴
>
> > y  复制 配合移动命令复制多行
> >
> > yy    复制当前行    nyy  复制多行
> >
> > p   粘贴
>
> ----------
>
> 

> 替换
>
> > r  替换当前字符
> >
> > R  替换当前单词
>
> 缩进
>
> > 选中文本 >>
>
> 查找
>
> > /str
> >
> > n 查找下一个
> >
> > N查找上一下
> >
> > \* 向后查找当前光标所在单词 
> >
> > \# 向前查找当前光标所在单词
>
> 替换
>
> > :%s/oldWorld/newWorld/g   全局替换
> >
> > :'<,'>s/oldWorld/newWorld/g    可是区域替换  先选中可视区域
> >
> > :%s/oldWorld/newWorld/gc   确认替换



##### 编辑模式

> 进入编辑模式
>
> > i  在当前字符串进入插入模式
> >
> > I  在当前行首进入插入模式
> >
> > a   在当前字符后添加文本
> >
> > A   在当前行末尾插入文本
> >
> > o   在当前行下一行插入空行
> >
> > O   在当前行上一行插入空白行



##### 末行模式

> `w`  保存文件
>
> `q!` 强制退出
>
> `q` 退出 ,如未保存不允许退出
>
> `wq`  保存并退出
>
> `x` 保存并退出



### shell

> Shell 脚本（shell script），是一种为 shell 编写的脚本程序
>
> 第一行指定解释器的位置    #!/bin/bash
>
> 在shell脚本所在目录运行   ./shelldemo.sh

#### 1.变量

> 变量名一般由数字、字母、下划线组成但不能以数字开头
>
> 等号两边不能有开头
>
> 变量名习惯大写

- 系统变量  $HOME  $PATH  $PWD  $SHELL  $USER  等

> 输出预定义变量：
>
> echo "PATH=$PATH"
> echo "user=$USER"
>
> set  输出所有的系统变量

- 自定义变量

> ```bash
> A=100   # 自定义变量
> echo "A=$A" # 输出自定义变量
> unset A  # 取消定义变量A
> echo "A=$A" # 输出变量A
> ```
>
> 静态变量不可被unset
>
> ```bash
> #!/bin/bash
> readonly A=linux
> echo A=$A
> unset A   # cannot unset: readonly variable
> echo a=$A
> ```
>
> 将命令的返回值赋给变量
>
> ```bash
> #!/bin/bash
> BB=`ls -l /home`
> CC=$(date)
> echo $BB $CC
> ```

#### 2.环境变量(/etc/profile)

> 设置环境变量
>
> export 变量名=变量值
>
> ```bash
> #!/bin/bash
> TOMCAT_HOME=/opt/tomcat
> export TOMCAT_HOME
> ```
>
> 执行脚本
>
> source /etc/profile    # 让配置信息立即生效
>
> echo $变量名

#### 3.位置参数变量

```linux
[root@root test]# cat demo.sh 
#!/bin/bash
echo $0 $1 $2  # $0 获取命令  $1获取位置1的参数  依次类推  超过10 ${10}
echo $*  # $* 把参数当成一个整体
echo $@  # $@
echo $#  # $# 参数的个数
[root@root test]# ./demo.sh a b
./demo.sh a b
a b
a b
2
```

#### 4.预定义的变量

> $$  获取当前进程号 PID
>
> $!  后台运行最后的一个进程号 PID
>
> $?  最后一次执行命令的状态  值为0 成功  非0 失败
>
> ```bash
> [root@izj6c1n90e758nubb3uipvz test]# cat demo.sh 
> #!/bin/bash
> echo "当前进程号是:$$"
> ./a.sh &    #  &  以后台运行的方式执行该脚本
> echo "最后执行的进程号是:$!"
> echo "最后的命令是否正确执行:$?" 
> [root@izj6c1n90e758nubb3uipvz test]# ./demo.sh 
> 当前进程号是:21566
> 最后执行的进程号是:21567
> 最后的命令是否正确执行:0
> [root@izj6c1n90e758nubb3uipvz test]# Sun Aug 15 09:22:43 CST 2021
> 
> ```

#### 5.运算符

```bash
[root@izj6c1n90e758nubb3uipvz test]# cat demo.sh 
#!/bin/bash
R1=$(((2+3)*4))
echo $R1
R2=$[(2+3)*4]
echo $R2
[root@izj6c1n90e758nubb3uipvz test]# ./demo.sh 
20
20
```

```bash
[root@izj6c1n90e758nubb3uipvz test]# cat demo.sh 
#!/bin/bash
R1=$[($1+$2)*$3]
echo $R1
[root@izj6c1n90e758nubb3uipvz test]# ./demo.sh 1 1 2
4
```

#### 6.流程控制

> ```bash
> <<EOF
> -d ->dirtionnary 判断目录是否存在
> 例： -d /tmp // 返回为真便是/tmp是文件夹
> 
> -e ->exist 判断是否存在
> 例： -e filename // 返回为真表示filename文件存在
> 
> -f ->file 判断是不是文件
> 例： -f file // 返回为真表示file是文件。
> 
> -n 来判定字符串非空
> 例： -n "${TEST}" 判断TEST是否为空
> EOF
> 
> 
> a=10
> b=20
> if [ $a == $b ]
> then
> echo "a 等于 b"
> elif [ $a -gt $b ]
> then
> echo "a 大于 b"
> elif [ $a -lt $b ]
> then
> echo "a 小于 b"
> else
> echo "没有符合的条件"
> fi
> ```
>
> ---
>
> ```bash
> for loop in 1 2 3 4 5
> do
>  echo "The value is: $loop"
> done
> ```
>
> ---
>
> ```bash
> while(( $int<=5 ))
> do
>  echo $int
>  let "int++"
> done
> ```
>
> ---
>
> ```bash
> echo '输入 1 到 4 之间的数字:'
> echo '你输入的数字为:'
> read aNum
> case $aNum in
>     1)  echo '你选择了 1'
>     ;;
>     2)  echo '你选择了 2'
>     ;;
>     3)  echo '你选择了 3'
>     ;;
>     4)  echo '你选择了 4'
>     ;;
>     *)  echo '你没有输入 1 到 4 之间的数字'
>     ;;
> esac
> ```



#### 7.read

```bash
[root@izj6c1n90e758nubb3uipvz test]# cat demo.sh 
#!/bin/bash
read -p "请输入协议:" PRO
echo "你输入的协议是:$PRO"
read -t 10 -p "请10s内输入端口" PORT
echo "你输入的端是:$PORT"
[root@izj6c1n90e758nubb3uipvz test]# ./demo.sh 
请输入协议:tcp
你输入的协议是:tcp
请10s内输入端口80
你输入的端是:80
```

#### 8.函数

- 系统函数

> basename  返回文件名
>
> ```linux
> [root@izj6c1n90e758nubb3uipvz test]# basename /home/test/demo.sh 
> demo.sh
> [root@izj6c1n90e758nubb3uipvz test]# basename /home/test/demo.sh .sh
> demo
> ```
>
> dirname  返回文件路径
>
> ```linux
> [root@izj6c1n90e758nubb3uipvz test]# dirname /home/test/demo.sh 
> /home/test
> ```

- 自定义函数

> ```bash
> [ function ] funname [()]
> 
> {
> 
>     action;
> 
>     [return int;]
> 
> }
> 
> [root@izj6c1n90e758nubb3uipvz test]# cat demo.sh 
> #!/bin/bash
> #!/bin/bash
> sum(){
> read N
> read M
> return $[($M+$N)]
> }
> sum
> echo "函数执行的结果是:$?"
> [root@izj6c1n90e758nubb3uipvz test]# ./demo.sh 
> 1
> 2
> 函数执行的结果是:3
> ```

---



#### 9. tip

##### 9.1 {} | ()

```bash
# {} 里面执行的命令 不会开启子进程,它的修改的内容会影响到当前shell
# 注意格式 { commond ; commond ; }
echo $BASHPID
14938
{ echo $BASHPID ; }
14938
# () 里面执行的命令 会开启子进程,它的修改的内容不会影响到当前shell
# 注意格式 ( commond ; commond )
( echo $BASHPID ; )
14970
```



##### 9.2 set  -ue

```bash
# 执行出错就退出,不允许使用未定义的变量
# 建议写在脚本首行
set -eu
```



##### 9.3 逻辑运算符号

```bash
[ -n "xxx" ] # 判断字符串是否为空,不是空返回真
# 以下是对文件、目录的判断
#-------------------------------------------
if [ -r ${FILE_PATH} ] then;
	echo "xxx"
fi
# -w	可写
# -x	可执行
# -u	是否拥有suid权限
# -g	是否拥有sgid权限
# -k	是否存在sticky权限   

#-----------------------------

# -a	等价于 -e
# -e	判断文件是否存在,存在即是真
# -b	判断未见是否为块设备
# -c	判断是否存在字符设备文件
# -d	判断是否为目录
# -f	判断是否为普通文件
# -h	判断是否为符号链接文件
# -p	判断是否为管道文件
# -s	判断是否为套接字文件

#-----------------------------

# -s	是否存在且非空
# -t	文件描述符是否在终端打开
# -N	判断文件是否在上一次读取后被修改过
# -O	判断有效用户是否为文件属主
# -G	判断有效用户是否为文件属组
#-------------------------------------------

# [ condition -a condition ] 等价  conditon  &&  conditon 
# [ condition -o condition ] 等价  conditon  ||  conditon 
# [ ! condition ] 等价 ! condition
```









### 附加

#### 1.iftop流量监控

> ```linux
> yum install flex byacc libpcap ncurses ncurses-devel libpcap-devel
> wget http://www.ex-parrot.com/pdw/iftop/download/iftop-0.17.tar.gz
> 
> tar zxvf iftop-0.17.tar.gz
> cd iftop-0.17
> ./configure
> make && make install
> iftop  # 运行iftop
> ```
>
> > 中间的<= =>这两个左右箭头，表示的是流量的方向。
> >
> > TX：发送流量
> > RX：接收流量
> > TOTAL：总流量
> > Cumm：运行iftop到目前时间的总流量
> > peak：流量峰值
> > rates：分别表示过去 2s 10s 40s 的平均流量
> >
> > ---
> >
> > 常用的参数
> >
> > -i设定监测的网卡，如：# iftop -i eth1
> >
> > -B 以bytes为单位显示流量(默认是bits)，如：# iftop -B
> >
> > -n使host信息默认直接都显示IP，如：# iftop -n
> >
> > -N使端口信息默认直接都显示端口号，如: # iftop -N
> >
> > -F显示特定网段的进出流量，如# iftop -F 10.10.1.0/24或# iftop -F 10.10.1.0/255.255.255.0
> >
> > -h（display this message），帮助，显示参数信息
> >
> > -p使用这个参数后，中间的列表显示的本地主机信息，出现了本机以外的IP信息;
> >
> > -b使流量图形条默认就显示;
> >
> > -f这个暂时还不太会用，过滤计算包用的;
> >
> > -P使host信息及端口信息默认就都显示;
> >
> > -m设置界面最上边的刻度的最大值，刻度分五个大段显示，例：# iftop -m 100M
> > 进入iftop画面后的一些操作命令(注意大小写)
> >
> > 按h切换是否显示帮助;
> >
> > 按n切换显示本机的IP或主机名;
> >
> > 按s切换是否显示本机的host信息;
> >
> > 按d切换是否显示远端目标主机的host信息;
> >
> > 按t切换显示格式为2行/1行/只显示发送流量/只显示接收流量;
> >
> > 按N切换显示端口号或端口服务名称;
> >
> > 按S切换是否显示本机的端口信息;
> >
> > 按D切换是否显示远端目标主机的端口信息;
> >
> > 按p切换是否显示端口信息;
> >
> > 按P切换暂停/继续显示;
> >
> > 按b切换是否显示平均流量图形条;
> >
> > 按B切换计算2秒或10秒或40秒内的平均流量;
> >
> > 按T切换是否显示每个连接的总流量;
> >
> > 按l打开屏幕过滤功能，输入要过滤的字符，比如ip,按回车后，屏幕就只显示这个IP相关的流量信息;
> >
> > 按L切换显示画面上边的刻度;刻度不同，流量图形条会有变化;
> >
> > 按j或按k可以向上或向下滚动屏幕显示的连接记录;
> >
> > 按1或2或3可以根据右侧显示的三列流量数据进行排序;
> >
> > 按<根据左边的本机名或IP排序;
> >
> > 按>根据远端目标主机的主机名或IP排序;
> >
> > 按o切换是否固定只显示当前的连接;
> >
> > 按q退出监控。





#### 2.Vray一键安装

```bash
bash <(wget -qO- -o- https://git.io/v2ray.sh)
https://github.com/Kejifaxian/welcome/  # 代理软件下载
```





#### 3.关闭防火墙

```bash
# 查看防火墙状态
systemctl status firewalld.service

# 关闭防火墙
systemctl stop firewalld.service

# 永久关闭防火墙
systemctl disable firewalld.service
```





#### 4.搭建代理服务器

1. v2ray

2. tinyproxy 

   > 作为爬虫的代理IP
   >
   > ```bash
   > # 安装tinyproxy
   > yum install tinyproxy
   > 
   > # 开启代理服务
   > systemctl start tinyproxy
   > 
   > # 查看服务状态
   > systemctl status tinyproxy.service
   > 
   > # 更改配置文件,允许所有外部主机访问
   > vim /etc/tinyproxy/tinyproxy.cong
   > #=====================
   > # 注释限制本地访问的命令
   > # #Allow 127.0.0.1
   > #=====================
   > 
   > # 重启服务
   > systemctl restart tinyproxy
   > 
   > # 在服务器端开放代理服务的端口 默认 8888
   > 
   > # 测试端口
   > telnet ip port
   > 
   > # 程序测试
   > `requests.get("http://httpbin.org/ip",proxies={"http":"http://ip:8888"}).text`
   > ```
   >



#### 5.搭建selenium

```bash
pip3 install selenium

# 安装google-chrome
wget https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm

# 查看安装的chrome版本
google-chrome --version

# 下载对应得到chromedriver驱动
# https://chromedriver.chromium.org/downloads

# 解压下载后的文件
unzip chromedriver_linux64.zip

# 移动文件解压后的文件 chromedriver /usr/bin
mv chromedriver /usr/bin

#------------------------------------------------

# 测试 
from selenium import webdriver, common


url = 'http://baidu.com'
from selenium.webdriver.chrome.options import Options
chrome_options = Options()
chrome_options.add_argument('--no-sandbox')
chrome_options.add_argument('--disable-dev-shm-usage')
chrome_options.add_argument('--headless')
browser = webdriver.Chrome(chrome_options=chrome_options)

browser.get(url)
print(browser.page_source)
browser.quit()
```



#### 6.日志

>systemd-journald是一个改进型日志管理服务，可以收集来自内核、系统早期启动阶段的日志、系统守护进程在启动和运行中的标准输出和错误信息，还有syslog的日志
>
>/var/log/messages 绝大多数的系统日志都记录到该文件
>/var/log/secure 所有跟安全和认证授权等日志都会记录到此文件
>/var/log/maillog 邮件服务的日志
>/var/log/cron crond计划任务的日志
>/var/log/boot.log 系统启动的相关日志
>
>/var/log/journal
>
>`rm -rf /var/log/journal/*`  暴力删除日志
>
>`systemctl restart systemd-journald.service`  重启日志服务
>
>`journalctl --verify` 检查日志运行状态

```bash
# 误删 /var/log/secure 解决办法
touch /var/log/secure
chmod 600 /var/log/secure
service sshd restart
service syslog restart # service rsyslog restart
```



#### 7. openVpn 基于证书

> 客户端下载地址
>
> 1.下载安装MacOS客户端 https://tunnelblick.net/downloads.html 选择Stable稳定版 Tunnelblick 3.7.8
>
> 安装成功后 双击client.ovpn（附件）
>
> 选择连接client 弹窗显示connected 则连接成功
>
> 2、 Windows系统 windows10 安装包 openvpn-install-2.4.7-I607-Win10.exe
>
> 下载并安装和系统对应版本的安装程序 https://openvpn.net/community-downloads/
>
> 安装成功后，打开应用，选择导入配置、导入client.ovpn文件
>
> 导入成功后，选择连接即可
>
> 3、Android系统 安装包：openvpn-connect-3-0-5.apk
>
> 下载安装客户端 https://openvpn-connect.en.uptodown.com/android 选择最新版本
>
> 安装成功后、点击client.ovpn, 选择使用openVPN connect 打开
>
> 进入app, 点击连接即可
>
> 4.linxu 系统安装openvpn客户端成功后：执行命令 :
>
> ```bash
> # 前台运行openvpn  ctrl+c 停止运行
> openvpn /etc/openvpn/client.ovpn
> 
> # 后台运行openvpn
> nohup openvpn /etc/openvpn/client.ovpn
> # 停止openvpn
> kill -9 $(ps aux|grep openvpn |awk 'NR==1{print $2}')
> ```
>
> 
>
> 
>
> ---
>
> 服务端搭建
>
> ```bash
> git clone git@github.com:xh-ss/shells.git
> 
> # 运行 20.sh 这个脚本,基于证书验证的方式
> # 协议选择UDP,其他的全部默认,默认开启端口1194
> bash 20.sh
> 
> # 如果服务器厂商默认开启了防火墙,则需要通过服务器网页控制台手动配置开启端口 1194,端口对应的协议选择UDP,如:
> <<EOF
> 应用类型	来源	协议	端口	策略 	备注	操作
> 自定义	0.0.0.0/0	UDP	1194	允许	openvpn
> EOF
> 
> # 执行脚本完毕后,默认生成证书 /root/client.ovpn  
> # 一份证书只能同时被一个客户端使用,要使用多个客户端则需要重新执行脚本 bash 20.sh 增加一个新的客户端证书
> 
> # 查看openvpn 进程信息
> netstat -antup |grep openvpn
> 
> # 停止服务
> kill -9 11580
> 
> # 重启服务
> /usr/sbin/openvpn --status /run/openvpn-server/status-server.log --status-version 2 --suppress-timestamps --config server.conf
> ```



#### 9. 删除虚拟网卡

```bash
# 查看网卡信息
ifconfig

# 安装 bridge-utils
yum install bridge-utils

# 删除虚拟网卡
ifconfig docker0 down 
brctl delbr docker0

# 删除虚拟桥接网卡
ip link delete br-4f875751b353
```



#### 10. vsftpd

> ftp 是一种文本传输协议，基于CS模式，默认开启两个端口20,21
>
> 20端口用于传输数据，21端口接受客户端的指令以及参数
>
> ftp两种工作模式：
>
> > 主动模式：ftp服务器主动向客户端发起连接请求
> >
> > 被动模式：ftp服务器等待客户端发起连接请求
>
> vsftp安装(实现ftp协议的软件):
>
> `yum install vsftpd -y`
>
> ---
>
> 配置文件解释：
>
> ```bash
> #vsftpd.user_list：位于/etc目录下。该文件里的用户账户在默认情况下也不能访问FTP服务器，仅当vsftpd.conf配置文件里启用userlist_enable=NO选项时才允许访问。
> 
> #vsftpd.conf：位于/etc/vsftpd目录下。来自定义用户登录控制、用户权限控制、超时设置、服务器功能选项、服务器性能选项、服务器响应消息等FTP服务器的配置。
> 
> #(1)用户登录控制
> anonymous_enable=YES #允许匿名用户登录。
> 
> no_anon_password=YES #匿名用户登录时不需要输入密码。
> 
> local_enable=YES #允许本地用户登录。
> 
> deny_email_enable=YES #可以创建一个文件保存某些匿名电子邮件的黑名单，以防止这些人使用Dos攻击。
> 
> banned_email_file=/etc/vsftpd.banned_emails，#当启用deny_email_enable功能时，所需的电子邮件黑名单保存路径(默认为/etc/vsftpd.banned_emails)。
> 
> #(2)用户权限控制
> 
> write_enable=YES #开启全局上传权限。
> 
> local_umask=022 #本地用户的上传文件的umask设为022(系统默认是077，一般都可以改为022)。
> 
> anon_upload_enable=YES #允许匿名用户具有上传权限，很明显，必须启用write_enable=YES，才可以使用此项。同时我们还必须建立一个允许ftp用户可以读写的目录(前面说过，ftp是匿名用户的映射用户账号)。
> 
> anon_mkdir_write_enable=YES #允许匿名用户有创建目录的权利。
> 
> chown_uploads=YES #启用此项，匿名上传文件的属主用户将改为别的用户账户，注意，这里建议不要指定root账号为匿名上传文件的属主用户！
> 
> chown_username=whoever #当启用chown_uploads=YES时，所指定的属主用户账号，此处的whoever自然要用合适的用户账号来代替。
> 
> chroot_list_enable=YES #可以用一个列表限定哪些本地用户只能在自己目录下活动，如果chroot_local_user=YES，那么这个列表里指定的用户是不受限制的。
> 
> chroot_list_file=/etc/vsftpd.chroot_list
> chroot_local_user=YES #则指定该列表(chroot_local_user)的保存路径(默认是/etc/vsftpd.chroot_list)。
> 
> nopriv_user=ftpsecure#指定一个安全用户账号，让FTP服务器用作完全隔离和没有特权的独立用户。这是vsftpd系统推荐选项。
> 
> async_abor_enable=YES#强烈建议不要启用该选项，否则将可能导致出错！
> 
> ascii_upload_enable=YES；ascii_download_enable=YES #默认情况下服务器会假装接受ASCⅡ模式请求但实际上是忽略这样的请求，启用上述的两个选项可以让服务器真正实现ASCⅡ模式的传输。
> 
> #注意：启用ascii_download_enable选项会让恶意远程用户们在ASCⅡ模式下用“SIZE/big/file”这样的指令大量消耗FTP服务器的I/O资源。
> 
> #这些ASCⅡ模式的设置选项分成上传和下载两个，这样我们就可以允许ASCⅡ模式的上传(可以防止上传脚本等恶意文件而导致崩溃)，而不会遭受拒绝服务攻击的危险。
> 
> #(3)用户连接和超时选项
> 
> idle_session_timeout=600 #可以设定默认的空闲超时时间，用户超过这段时间不动作将被服务器踢出。
> 
> data_connection_timeout=120 #设定默认的数据连接超时时间。
> 
> #(4)服务器日志和欢迎信息
> 
> dirmessage_enable=YES #允许为目录配置显示信息，显示每个目录下面的message_file文件的内容。
> 
> ftpd_banner=Welcometo blah FTP service #可以自定义FTP用户登录到服务器所看到的欢迎信息。
> 
> xferlog_enable=YES #启用记录上传/下载活动日志功能。
> 
> xferlog_file=/var/log/vsftpd.log #可以自定义日志文件的保存路径和文件名，默认是/var/log/vsftpd.log。
> 
> anonymous_enable=YES #允许匿名登录
> local_enable=YES #允许本地用户登录
> 
> write_enable=YES #开放本地用户写权限
> 
> local_umask=022 #设置本地用户生成文件的掩码为022
> 
> #anon_upload_enable=YES此项设置允许匿名用户上传文件
> 
> #anon_mkdir_write_enable=YES开启匿名用户的写和创建目录的权限
> 
> dirmessage_enable=YES #当切换到目录时，显示该目录下的.message隐藏文件的内容
> 
> xferlog_enable=YES #激活上传和下载日志
> 
> connect_from_port_20=YES #启用FTP数据端口的连接请求
> 
> #chown_uploads=YES是否具有上传权限.用户由chown_username参数指定。
> 
> #chown_username=whoever指定拥有上传文件权限的用户。此参数与chown_uploads联用。
> 
> #xferlog_file=/var/log/vsftpd.log
> 
> xferlog_std_format=YES #使用标准的ftpdxferlog日志格式
> 
> #idle_session_timeout=600此设置将在用户会话空闲10分钟后被中断
> 
> #data_connection_timeout=120将在数据连接空闲2分钟后被中断
> 
> #ascii_upload_enable=YES启用上传的ASCII传输方式
> 
> #ascii_download_enable=YES启用下载的ASCII传输方式
> 
> #ftpd_banner=Welcometo blah FTP service 设置用户连接服务器后显示消息
> 
> #deny_email_enable=NO此参数默认值为NO。当值为YES时，拒绝使用banned_email_file参数指定文件中所列出的e-mail地址用户登录。
> 
> #banned_email_file=/etc/vsftpd.banned_emails指定包含拒绝的e-mail地址的文件.
> 
> #chroot_list_enable=YES设置本地用户登录后不能切换到自家目录以外的别的目录
> 
> #chroot_list_file=/etc/vsftpd.chroot_list
> 
> #ls_recurse_enable=YES
> 
> pam_service_name=vsftpd #设置PAM认证服务的配置文件名称，该文件存放在/etc/pam.d/
> ```

##### 10.1初始化配置文件

```bash
# 复制的时候 去掉所有的注释信息 也不要留多余的空格，否则服务无法正常开启
# vim /etc/vsftpd/vsftpd.conf
anonymous_enable=YES   # 允许匿名用户登录
local_enable=YES   # 允许本地shell用户登录
write_enable=YES  # 允许用户写入数据
local_umask=022  # 用户上传文件权限的mask值
dirmessage_enable=YES   # 显示目录信息
xferlog_enable=YES   # 开启上传下载日志信息
xferlog_file=/var/log/xferlog   # 取消默认注释，打开日志文件记录的位置
connect_from_port_20=YES   #
xferlog_std_format=YES
listen=YES   # 开启IPV4监听
listen_ipv6=NO  # 关闭IPV6监听

pam_service_name=vsftpd
userlist_enable=YES  
tcp_wrappers=YES

# 开启被动连接模式
pasv_min_port=50000
pasv_max_port=50010
pasv_address=43.128.111.99
pasv_enable=yes
```

##### 10.2 三种登录方式

```bash
<<EOF
用户认证有三种模式：
1.匿名登录 任何人都不需要账号(anonymous)密码(null)都可以登录
2.本地用户模式，基于ftp服务器linux用户账户密码认证，配置简单，单存在安全隐患
3.虚拟用户登录模式，单独为ftp工具创建用户数据库，基于口令验证账号密码信息，比较安全
EOF

# 1.配置匿名登录
# 在上面默认的配置下新增 匿名登录配置信息
#-------------------

# 这一行默认已经配置
# 开启匿名登录
anonymous_enable=YES

# 新增 
# 允许匿名用户上传文件夹
anon_upload_enable=YES
# 允许匿名用户写入文件夹
anon_mkdir_write_enable=YES
# 允许匿名用户修改文件夹
anon_other_write_enable=YES

#-------------------

systemctl restart vsftpd.service
#强制修改目录下所有文件的拥有者是 ftp用户
chown -Rf ftp /var/ftp/pub
#强制修改目录下所有文件是ftp组的ftp成员
chown -Rf ftp.ftp /var/ftp/pub  
>>EOF
客户端匿名登录流程
hxh@hxh-machine:~$ ftp 43.128.111.99
Connected to 43.128.111.99.
220 (vsFTPd 3.0.2)
Name (43.128.111.99:hxh): anonymous  # 匿名用户统一用户名
331 Please specify the password.
Password: # 不需要密码 直接回车
230 Login successful.
EOF

#------------------------------------------------------

# 2.配置本地用户登录
# ftp本地用户登录后默认进入 用户家目录
# 基于上面默认配置做修改
#------------------------
# 注释掉 匿名用户登录开启的选项
# anonymous_enable=YES
# anon_upload_enable=YES
# anon_mkdir_write_enable=YES
# anon_other_write_enable=YES


# 这两行默认已经配置
# 开启本地用户登录
local_enable=YES
userlist_enable=YES

userlist_deny=YES
#------------------------

<EOF
hxh@hxh-machine:~$ ftp 43.128.111.99
Connected to 43.128.111.99.
220 (vsFTPd 3.0.2)
Name (43.128.111.99:hxh): hxh
331 Please specify the password.
Password:  # 输入shell用户hxh 的密码
230 Login successful.
EOF

# 禁止指定用户登录
vim /etc/vsftpd/ftpusers
# 一行输入一个用户名 表示禁止该用户登录ftp服务器
hxh
#------------------------------------------------------


# 配置虚拟用户登录
# 1.安装数据库加密软件包
yum install db4 db4-utils -y

# 2.编辑虚拟用户信息
# 第一行用户名,第二行密码，第三行用户名，第四行密码.....
vim /etc/vsftpd/ftp_user.txt
#------------------
alpha
qwer
test
qwer
#------------------

# 3.加密存放虚拟用户信息的文件 生成新的加密文件
db_load -T -t hash -f /etc/vsftpd/ftp_user.txt /etc/vsftpd/ftp_user.db
# 修改用户信息的加密文件的权限
chmod 600 /etc/vsftpd/ftp_user.db
# 删除原文件
rm -rf /etc/vsftpd/ftp_user.txt

# 4.创建一个系统用户和虚拟用户做映射，但不需要家目录，且禁止系统用户登录shell
useradd -d /var/ftp/ftpdir -s /sbin/nologin virtual_ftp
chmod 755 /var/ftp/ftpdir
echo "virtual_ftp" >> /etc/vsftpd/ftpusers

# 5.修改vsftpd的配置文件，添加支持虚拟用户验证的PAM文件，PAM是一组安全机制的模块
vim /etc/pam.d/vsftpd   # 覆盖文件,注释系统自带的配置，新增自定义两行
#----------------------------------
#%PAM-1.0
#session    optional     pam_keyinit.so    force revoke
#auth       required	pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
#auth       required	pam_shells.so
#auth       include	password-auth
#account    include	password-auth
#session    required     pam_loginuid.so
#session    include	password-auth

auth required pam_userdb.so db=/etc/vsftpd/ftp_user
account required pam_userdb.so db=/etc/vsftpd/ftp_user
#----------------------------------

# 6.编辑配置文件 开启虚拟用户的支持
vim /etc/vsftpd/vsftpd.conf
#-----------------------------------
# 上面默认配置已开启这一行
pam_service_name=vsftpd
guest_enable=YES
# 创建的虚拟用户的名称
guest_username=virtual_ftp
# 允许用户写入
allow_writeable_chroot=YES
#-----------------------------------

# 7.针对不同的用户设置不同的权限
mkdir /etc/vsftpd/virtual_user_dir
cd /etc/vsftpd/virtual_user_dir
vim alpha
#-----------
# 允许alpha用户上传文件，创建、修改文件夹
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
#-----------

vim test
#-----------
# 不允许test用户上传文件，创建、修改文件夹，只能读取文件
anon_upload_enable=NO
anon_mkdir_write_enable=NO
anon_other_write_enable=NO
#-----------

# 8.修改主配置文件 加载权限配置文件
vim vsftpd.conf # 追加这一行
#------------------
user_config_dir=/etc/vsftpd/virtual_user_dir
#------------------
```





### ubuntu

#### 1.使用用笔记

##### 1.1.禁止设备自动挂载

```bash
#禁止自动挂载：
gsettings set org.gnome.desktop.media-handling automount false
#禁止自动挂载并打开
gsettings set org.gnome.desktop.media-handling automount-open false
#允许自动挂载
gsettings set org.gnome.desktop.media-handlingautomount true
#允许自动挂载并打开
gsettings set org.gnome.desktop.media-handling automount-open true
```



##### 1.2卸载自带软件

```bash
# uninstall Browser
sudo apt-get remove --purge webbrowser-app
 
# uninstall Videos
sudo apt-get remove --purge totem*
 
# uninstall Document Viewer
sudo apt-get remove --purge evince
 
# uninstall games
sudo apt-get remove --purge aisleriot
sudo apt-get remove --purge gnome-mahjongg
sudo apt-get remove --purge gnome-mines
sudo apt-get remove --purge gnome-sudoku
 
# uninstall transmission Bit
sudo apt-get remove --purge transmission-*
 
# uninstall cheese webcam booth
sudo apt-get remove --purge cheese
 
# uninstall rhythmbox
sudo apt-get remove --purge rhythmbox*
```



##### 1.3 安装python虚拟环境

```bash
# 更换软件源
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 需要pip安装
pip install virtualenv
pip install virtualenvwrapper

#-------------------------------------------
# 配置环境变量
mkdir $HOME/.virtualenvs # 虚拟环境存放的目录
export WORKON_HOME=$HOME/.virtualenvs

# 将这行命令加入到配置文件 ~/.bashrc 中
vim ~/.bashrc
#-------------------
source /usr/local/bin/virtualenvwrapper.sh
#-------------------
# 刷新配置文件
source ~/.bashrc

#-------------------------------------------
# 使用虚拟环境
# mkvirtualenv [虚拟环境名称]
# workon [虚拟环境名称]
# deactivate [虚拟环境名称]
# rmvirtualenv [虚拟环境名称]

# python版本跟pip不一致,卸载pip 重新安装
# python -m pip uninstall pip 卸载pip
```



##### 1.4 linux配置文件区别

|                                        | 影响范围                 | 加载               |
| -------------------------------------- | ------------------------ | ------------------ |
| **/etc/profile**       /etc/profile.d/ | 系统（对所有用户起作用） | 系统登录后         |
| **~/.profile**                         | 用户                     | **启动bash shell** |
| **/etc/bash.bashrc**                   | 系统                     | 系统登录后         |
| **~/.bashrc**                          | 用户                     | **启动bash shell** |

```bash
<<EOF
/etc/profile，/etc/bashrc 是系统全局环境变量设定
~/.profile，~/.bashrc用户家目录下的私有环境变量设定
当登入系统时候获得一个shell进程时，其读取环境设定档有三步
1首先读入的是全局环境变量设定档/etc/profile，然后根据其内容读取额外的设定的文档，如
/etc/profile.d和/etc/inputrc
2然后根据不同使用者帐号，去其家目录读取~/.bash_profile，如果这读取不了就读取~/.bash_login，这个也读取不了才会读取
~/.profile，这三个文档设定基本上是一样的，读取有优先关系
3然后在根据用户帐号读取~/.bashrc
至于~/.profile与~/.bashrc的不区别
都具有个性化定制功能
~/.profile可以设定本用户专有的路径，环境变量，等，它只能登入的时候执行一次
~/.bashrc也是某用户专有设定文档，可以设定路径，命令别名，每次shell script的执行都会使用它一次
EOF

cat /etc/profile
cat /etc/bash.bashrc
cat ~/.profile
cat ~/.bashrc
```



##### 1.5 删除无效软件源

```bash
# 无法安全地用该源进行更新,所以默认禁用该源
cd /etc/apt/sources.list.d
ll /etc/apt/sources.list.d
# 哪个软件更新源报错删除哪个
sudo rm mystic-mirage-ubuntu-pycharm-bionic.list

# 更换软件源
# https://blog.csdn.net/qq_43115981/article/details/129062626
```



##### 1.6 安装node

```bash
# 1.进入 /usr/local 文件夹并下载
cd /usr/local
wget https://nodejs.org/dist/v18.12.1/node-v18.12.1-linux-x64.tar.xz

# 2.解压
xz -d node-v18.12.1-linux-x64.tar.xz
tar -xvf node-v18.12.1-linux-x64.tar

# 3.重命名
mv node-v18.12.1-linux-x64 node

# 4.配置系统环境看量
vim /etc/profile
#-------------------------------------
#node
export NODE_HOME=/usr/local/node 
export PATH=$NODE_HOME/bin:$PATH
#-------------------------------------

# 5.执行source才能使环境变量立即有效
source /etc/profile

# 6.安装成功后，查看对应版本信息
node -v 
npm -v

# 7.配置软连接，使全局都可以使用node命令
ln -s /usr/local/node/bin/node /usr/bin/node  # 将node源文件映射到usr/bin下的node文件
ln -s /usr/local/node/bin/npm /usr/bin/npm


# 安装 yarn
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update
sudo apt-get install yarn
```



##### 1.7 ssh免密登录

> SSH是目前比较可靠的专为远程登录会话和其他网络服务提供安全的协议。不同主机之间在进行通信时，一般都是需要输入密码进行验证， ssh免密码之后，只要通过指定主机地址和端口号就可以实现不同的计算机之间访问时，不需要密码实现直接访问。ssh免密码登录主要采用算法有：对称加密算法和非对称加密算法。例如有两个linux系统simple01和simple02：
> 1.首先在simple01上ssh-keygen -t rsa生成一对钥匙id_rsa（私钥）和id_rsa.pub（公钥）
> 2.simple01执行ssh-copy-id simple02 在simple02端生成autherired_keys文件并保存了simple01机器上的id_rsa.pub信息内容
> 3.simple01向simple02发送一个ssh请求
> 4.请求在传输中带有simple01机器发送的指令信息，指令信息如（root@simple01+指令等）
> 5.simple02接收到simple01的信息后，会根据（root@simple01+指令等)到authorized_keys中进行查找，如果没找到，向simple01返回需要输入密码，如果找到simple02会随机生成一个字符串，自已保存一份，然后使用simple01的公钥进行加密。
> 6.将加密后的字符串发送给simple01
> 7.simple01接收到加密后的字符串，用自已的私钥进行解密。
> 8.将解密后的字符串再发送给simple02
> 9.simple02接收simple01发送的解密后的字符串与原来的字符串进行比对，如果一致，可以免密码登录

```bash
# 进入ssh密钥对保存的目录
cd ~/.ssh

# 生成密钥对 id_rsa(私钥)   id_rsa_pub(公钥)
# 期间按三次回车,生成密钥对
# -f 指定生成密钥的文件名  不指定的话默认就是 id_rsa
ssh-keygen -t rsa -f id_github

# 将公钥上传到需要免密登录的服务器, 提示输入root用户的登录密码
# -i 是指定密钥对保存的位置 root用户名 192.169.1.1 目标IP  端口默认22
ssh-copy-id -i ./id_rsa.pub hxh@192.169.1.1  # 建议使用非 root 用户 防止暴力破解密码

ssh-add -k ./id_github  # mac系统需要加上这一步
# 免密登录服务器
ssh hxh@192.168.0.1

# 查看服务器保存的公钥
cd ~/.ssh
cat authorized_keys

# SSH进行认证的过程中除了对用户目录有权限要求外，对服务器上的 .ssh 文件夹和 authorized_keys 文件同样也要限制，如果日志中提示这两个的问题，可以通过如下方式进行修改：
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

<<EOF
另外用 Mac OSX Terminal ssh 命令连接服务器之后，如果一段时间不操作，就会退出登录并出现如下错误提示：
Write failed: Broken pipe
只能重新用 ssh 命令进行连接。
解决方法
方法一：$ ssh -o ServerAliveInterval=60 user@IP

方法二：只需在服务器的 /etc/ssh/sshd_config 中添加如下的配置：

添加或者修改ClientAliveInterval为“ClientAliveInterval 60”。这个参数的是意思是每1分钟，服务器向客户端发一个消息，用于保持连接。保存后记得重启ssh服务
EOF
# 移除免密登录,可以直接删除 authorized_keys(可保存多个服务器的公钥) 文件保存的指定服务器公钥的内容
```



##### 1.8  mysql配置主从同步

```bash
<<EOF
1.Mysql的主从同步就是当master（主库）发生数据变化的时候，会实时同步到slave（从库）。
2.主从复制可以水平扩展数据库的负载能力，容错，高可用，数据备份。
3.不管是delete、update、insert，还是创建函数、存储过程，都是在master上，当master有操作的时候，slave会快速的接受到这些操作，从而做同步

mysql主从复制用途
  ●实时灾备，用于故障切换
  ●读写分离，提供查询服务
  ●备份，避免影响业务
主从部署必要条件：
  ●主库开启binlog日志（设置log-bin参数）
  ●主从server-id不同
  ●从库服务器能连通主库
EOF

# 为了方便测试 使用docker安装2个mysql服务,分别是主从服务器 mysql01主服务器负责写入数据 ，mysql02从服务器负责读取数据
# 为了mysql服务器通信，让它们处于同一个网桥
# 安装mysql01   mysql02
docker run -d -p 3306:3306 --name mysql01 --network=mysql01_network -e MYSQL_ROOT_PASSWORD=root mysql:5.7.42
docker run -d -p 3306:3306 --name mysql02 --network=mysql01_network -e MYSQL_ROOT_PASSWORD=root mysql:5.7.42

# 进入容器 mysql01 配置相关信息
docker exec -it mysql01 bash
mysql -uroot -proot

vim /etc/mysql/my.cnf # 没有my.cnf这个文件就自己创建
#-------------------------
[mysqld]
server-id=1 # id唯一 
log-bin=mysql-bin # ## 开启二进制日志功能，可以随便取（关键）
read-only=0 # 0代表可读可写 1代表只读
#-------------------------

# 退出容器 重启mysql01服务 加载配置文件
docker restart mysql01

# 进入mysql01容器,新增slave用户(同步数据的用户)，并赋予相关权限
docker exec -it mysql01 bash
mysql -uroot -proot

CREATE USER 'slave'@'%' IDENTIFIED BY '123456';
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'%';
#---------------------------------------------------


# 进入容器 mysql02 配置相关信息
docker exec -it mysql02 bash
mysql -uroot -proot

vim /etc/mysql/my.cnf # 没有my.cnf这个文件就自己创建
#-------------------------
[mysqld]
server-id=2  
log-bin=mysql-slave-bin   # 开启二进制日志功能，以备Slave作为其它Slave的Master时使用
relay_log=edu-mysql-relay-bin # relay_log配置中继日志
read-only=1
#-------------------------
# 退出容器 重启mysql02服务 加载配置文件
docker restart mysql02

#---------------------------------------------------

# 进入mysql01容器
docker exec -it mysql01 bash
mysql -uroot -proot

show master status;
mysql> show master status; # 记住File 和 Position 对应的值 ，需要在从服务器mysql02中配置
# +------------------+----------+--------------+------------------+-------------------+
# | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
# +------------------+----------+--------------+------------------+-------------------+
# | mysql-bin.000001 |     1327 |              |                  |                   |
# +------------------+----------+--------------+------------------+-------------------+
# 1 row in set (0.00 sec)

# 进入mysql02容器,创建主从服务
docker exec -it mysql02 bash
mysql -uroot -proot

# master_host 主服务器IP地址
# master_user 同步数据的用户名
# master_password 同步数据的密码
# master_port 主服务器暴露的端口
# master_log_file 主服务器 File 对应的值
# master_log_pos 主服务器 Position 对应的值
# master_connect_retry 连接失败重试的次数
change master to master_host='192.168.3.10', master_user='slave', master_password='123456', master_port=3306, master_log_file='mysql-bin.000001', master_log_pos= 1327, master_connect_retry=30;

# 查询从服务器状态
show slave status \G;  # Slave_IO_Running 和 Slave_SQL_Running 对应的值是yes表示 成功同步
#---------------------------------------------------
  Read_Master_Log_Pos: 1327
               Relay_Log_File: edu-mysql-relay-bin.000002
                Relay_Log_Pos: 1030
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
#---------------------------------------------------

# 如果上面两个值都是NO 则需要开启在mysql02(从服务器)主从服务
start slave

# 测试 在主服务mysql01创建一个数据库然后查看mysql02的是否同步
# -----省略
create database test; # mysql01
# -----省略
show databases; # mysql02
```



##### 1.9 卸载自带软件

```bash
# apt remove --purge firefox*
apt remove --purge libreoffice*
sudo apt-get -y purge thunderbird*
sudo apt-get -y purge deja-dup
sudo apt-get -y purge simple-scan
sudo apt-get -y purge hplip*
sudo apt-get -y purge printer-driver*
sudo apt-get -y purge gnome-mahjongg
sudo apt-get -y purge gnome-mines
```



##### 1.10 更换软件源

```bash
# 备份原软件源
cp /etc/apt/sources.list /etc/apt/sources.list.backup

vim /etc/apt/sources.list
#------------------------------

# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse

# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse
# deb-src http://security.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse

#------------------------------
```



##### 1.11 安装java

```bash
# 下载jdk
# 移动jdk到 /usr/lib目录下
mv jdk-8u321-linux-x64.tar.gz  /usr/lib
mkdir jdk
tar -zxvf jdk-8u321-linux-x64.tar.gz
mv jdk1.8.0_321 /usr/lib/jdk

# 配置环境变量
vim /etc/profile
#--------------------------------
#set java11 env
export JAVA_HOME=/usr/lib/jvm/jdk-11  
export CLASSPATH=.:${JAVA_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH
#--------------------------------
# 刷新配置文件
source /etc/profile

# 创建软链接
sudo update-alternatives --install /usr/bin/java  java  /usr/lib/jdk/jdk1.8.0_321/bin/java 300   
sudo update-alternatives --install /usr/bin/javac  javac  /usr/lib/jdk/jdk1.8.0_321/bin/javac 300

# 检查
java -version
```



##### 1.12 maven

```bash
# 下载maven到指定目录 /opt
# 解压
tar -zxvf apache-maven-3.8.8-bin.tar.gz 

# 配置环境变量
vim /etc/profile
#--------------------------------
#set maven env
export M2_HOME=/opt/apache-maven-3.8.8
export CLASSPATH=$CLASSPATH:$M2_HOME/lib
export PATH=$PATH:$M2_HOME/bin
#--------------------------------
# 刷新配置文件
source /etc/profile

# 检查
mvn -v

# 修改远程仓库地址
<mirrors>
    <mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
	</mirror>
</mirrors>
```



##### 1.13 防火墙

```bash
# 安装防火墙
sudo sudo apt install ufw

# 查看防火墙状态(制定的规则)
sudo ufw status

# 删除防火墙规则
sudo ufw status numbered # 显示规则序号
sudo ufw delete 1 # 删除序号对应的规则

# 查看防火墙版本
sudo ufw version

# 开启防火墙
sudo ufw enable

# 关闭防火墙
sudo ufw disable

# 重启防火墙
sudo ufw reload

# 默认允许外部访问本机
sudo ufw default allow

# 默认拒绝外部访问主机,拒接所有外来访问，本机能正常访问外部。
sudo ufw default deny

# 允许外部访问53端口
sudo ufw allow 53 && sudo ufw reload

# 查看53端口的监听状态：
sudo netstat -tunlp | grep 53  # 查看53端口信息

# 拒绝外部访问53端口
sudo ufw deny 53

# 允许某个IP地址访问本机所有端口
sudo ufw allow from 192.168.0.1

# 拒绝某个网络地址的访问
ufw deny from 43.128.0.0/16

# 查看所有tcp,udp开放端口
netstat -ntulp

# 查看指定端口是否开放
lsof -i :80
```



##### 1.14 设置静态IP

```bash
# 查看网卡地址
ifconfig # enp3s0

# 备份旧网卡配置信息
cp /etc/netplan/01-network-manager-all.yaml /etc/netplan/01-network-manager-all.yaml.backup

# 查看网关信息
netstat -rn # 得到网关信息 192.168.3.1 子网掩码 255.255.255.0

# 配置静态IP
vim /etc/netplan/01-network-manager-all.yaml
#----------------------------
# Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
  ethernets:
          enp3s0:  # ifconfig 查询得到网卡名称
                  addresses: [192.168.3.8/24] # 静态IP地址 子网掩码:/24 表示 255.255.255.0  /16 表示255.255.0.0
                  gateway4: 192.168.3.1 # 配置网关信息
                  dhcp4: false # 关闭dhcp自动分配IP协议
                  nameservers:
                          addresses: [223.5.5.5, 192.168.3.1] # 配置dns服务器地址
#----------------------------

# 应用配置信息,重新然后查询
netplan apply
ifconfig

# 查看本机DNS信息
systemd-resolve --status | grep 'DNS Servers' -A2

# 刷新dns缓存
resolvectl flush-caches

# 查看dns缓存信息
resolvectl statistics
```



##### 1.15 配置开机自启任务

```bash
# 开启开机自启服务 ( unbuntu )
cp /lib/systemd/system/rc-local.service /lib/systemd/system/rc-local.service.backup

# 末行追加以下内容
vim /lib/systemd/system/rc-local.service
#-------------
[Install]
WantedBy=multi-user.target
#-------------

# 添加开机任务
vim /etc/rc.local
#-------------
#!/bin/bash
touch /root/a.txt
#-------------#
# 重启开机服务
systemctl start rc-local

#-----------------------------------------

# 开机会自动执行 /etc/profile.d 目录下的所有脚本
vim /etc/profile.d/my_bootStart_task.sh
#---------------
#!/usr/bin/bash
touch /home/hxh/a.txt
#--------------

# 检查是否有语法错误
bash -n /etc/profile.d/my_bootStart_task.sh
```



##### 1.16 进入恢复模式

```bash
# 因为误操作配置文件导致进入系统失败,需要进入恢复模式进行修改 (由于发行版差异,选项只有小部分版本不一样,流程都是差不多的)
# 1.开机进入系统前按 ESC  进入界面 选择 **Advanced options for Ubuntu 
# 2.进入界面后选择 Ubuntu, with Linux 4.15.0-72-generic (recovery mode)
# 3.界面会出现以下几个选项,根据需求做选择
<<EOF
Recovery Mode：Clean  恢复模式下Ubuntu用户可以使用的“Dpkg”选项允许修复和卸载导致系统出现问题的任何可能损坏的软件包
Recovery Mode：Fsck   恢复模式下的Fsck功能可以通过扫描损坏的硬盘并删除它们来快速修复损坏的硬盘，此功能非常有用
Recovery Mode：Root  有时由于配置错误等原因，系统可能无法正常启动,你无需记住密码,选择此项，然后按Enter，进入界面执行其他命令(修复,删除错误配置文件等)
EOF
```



##### 1.17 scrcpy使用

```bash
# 安装scrcpy  (https://github.com/Genymobile/scrcpy)
<<<EOF
Debian/Ubuntu: apt install scrcpy
Arch Linux: pacman -S scrcpy
Fedora: dnf copr enable zeno/scrcpy && dnf install scrcpy
Gentoo: emerge scrcpy
Snap: snap install scrcpy
EOF

# 1.有线连接:手机USB连接电脑,并且开启调试模式,文件传输模式

# 2.运行scrcpy,终端输入命令
scrcpy

# ==================================

# 无线连接(手机与电脑处于同一局域网): 手机USB连接电脑,并且开启调试模式,文件传输模式
# 1.开启手机设备监听指定端口,成功后会显示信息:restarting in TCP mode port: 5555  这个时候就可以拔掉手机的USB连接线
adb tcpip 5555

# 2. 终端输入命令开始连接手机,手机必须开启调试模式 (192.168.3.2 是手机的局域网IP)
adb connect 192.168.3.2:5555  # 取消已建立的连接  adb disconnect 192.168.3.2:5555

# 3.运行scrcpy
scrcpy

# ==================================

#常用命令
<<<EOF
查看连接设备: adb devices
快捷帮助：scrcpy -h
窗口标题：scrcpy --window-title “hxh” (“hxh” 为自定义)
显示全屏：scrcpy -f (或scrcpy --fullscreen）
屏幕关闭：scrcpy -S （启动镜像时关闭设备屏幕）
窗口无边界：scrcpy --window-borderless
窗口回转：scrcpy --rotation 3 (0:不旋转；1:逆时针旋转90度；2:旋转180度；3:顺时针旋转90度)
显示触摸轨迹：scrcpy -t (或scrcpy --show-touches）
录制视频文件：scrcpy -r hxh.mp4 (“hxh” 为自定义视频标题名，录制完成使用“ctrl + c“中断后会在 cmd 当前目录下生成该“hxh.mp4”录制文件，另外也可以使用录制后缀为“hxh.mkv”文件录制)
不投屏只录屏：scrcpy -Nr hxh.mp4（禁用镜像电脑端不显示，无感知，结束“ctrl + c“中断即可保存）
终止断开连接：adb kill-server
EOF
```



##### 1.18  apt 安装软件时出现问题

```bash
#1.apt安装软件时出现问题 以下包具有未满足的依赖项：    解决的方法

#方法一，安装aptitude包管理工具，解决依赖包
apt install aptitude
aptitude install 软件包   # aptitude install ffmpeg

#方法二，自动安装所需的依赖包，解决依赖包
apt --fix-broken install

#方法三，一键修复依赖包
apt install -f

```



##### 1.19 声卡关闭节能模式

```bash
echo 0 > /sys/module/snd_hda_intel/parameters/power_save
```





#### 2.常用软件

##### 2.1 git配置

```bash
vim ~/.gitconfig
#--------------------------------
[user]
	email = linux.hxh@outlook.com.com
	name = hxh
[core]
	excludesfile = ~/.gitignore # 全局忽略文件所在的路径
#--------------------------------

vim ~/.gitignore
#--------------------------------

# ----------------------------------  java  -----------------------------
# Compiled class file
*.class

# Log file
*.log

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files #
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar

# virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
hs_err_pid*
replay_pid*
# ----------------------------------  java  -----------------------------




# ----------------------------------  python  -----------------------------
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
share/python-wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# PyInstaller
#  Usually these files are written by a python script from a template
#  before PyInstaller builds the exe, so as to inject date/other infos into it.
*.manifest
*.spec

# Installer logs
pip-log.txt
pip-delete-this-directory.txt

# Unit test / coverage reports
htmlcov/
.tox/
.nox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover
*.py,cover
.hypothesis/
.pytest_cache/
cover/

# Translations
*.mo
*.pot

# Django stuff:
*.log
local_settings.py
db.sqlite3
db.sqlite3-journal

# Flask stuff:
instance/
.webassets-cache

# Scrapy stuff:
.scrapy

# Sphinx documentation
docs/_build/

# PyBuilder
.pybuilder/
target/

# Jupyter Notebook
.ipynb_checkpoints

# IPython
profile_default/
ipython_config.py

# pyenv
#   For a library or package, you might want to ignore these files since the code is
#   intended to run in multiple environments; otherwise, check them in:
# .python-version

# pipenv
#   According to pypa/pipenv#598, it is recommended to include Pipfile.lock in version control.
#   However, in case of collaboration, if having platform-specific dependencies or dependencies
#   having no cross-platform support, pipenv may install dependencies that don't work, or not
#   install all needed dependencies.
#Pipfile.lock

# poetry
#   Similar to Pipfile.lock, it is generally recommended to include poetry.lock in version control.
#   This is especially recommended for binary packages to ensure reproducibility, and is more
#   commonly ignored for libraries.
#   https://python-poetry.org/docs/basic-usage/#commit-your-poetrylock-file-to-version-control
#poetry.lock

# pdm
#   Similar to Pipfile.lock, it is generally recommended to include pdm.lock in version control.
#pdm.lock
#   pdm stores project-wide configurations in .pdm.toml, but it is recommended to not include it
#   in version control.
#   https://pdm.fming.dev/#use-with-ide
.pdm.toml

# PEP 582; used by e.g. github.com/David-OConnor/pyflow and github.com/pdm-project/pdm
__pypackages__/

# Celery stuff
celerybeat-schedule
celerybeat.pid

# SageMath parsed files
*.sage.py

# Environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

# Spyder project settings
.spyderproject
.spyproject

# Rope project settings
.ropeproject

# mkdocs documentation
/site

# mypy
.mypy_cache/
.dmypy.json
dmypy.json

# Pyre type checker
.pyre/

# pytype static type analyzer
.pytype/

# Cython debug symbols
cython_debug/
# ----------------------------------  python  -----------------------------




# ----------------------------------  JetBrains  -----------------------------
# Covers JetBrains IDEs: IntelliJ, RubyMine, PhpStorm, AppCode, PyCharm, CLion, Android Studio, WebStorm and Rider
# Reference: https://intellij-support.jetbrains.com/hc/en-us/articles/206544839

# User-specific stuff
.idea/**/workspace.xml
.idea/**/tasks.xml
.idea/**/usage.statistics.xml
.idea/**/dictionaries
.idea/**/shelf

# AWS User-specific
.idea/**/aws.xml

# Generated files
.idea/**/contentModel.xml

# Sensitive or high-churn files
.idea/**/dataSources/
.idea/**/dataSources.ids
.idea/**/dataSources.local.xml
.idea/**/sqlDataSources.xml
.idea/**/dynamic.xml
.idea/**/uiDesigner.xml
.idea/**/dbnavigator.xml

# Gradle
.idea/**/gradle.xml
.idea/**/libraries

# Gradle and Maven with auto-import
# When using Gradle or Maven with auto-import, you should exclude module files,
# since they will be recreated, and may cause churn.  Uncomment if using
# auto-import.
# .idea/artifacts
# .idea/compiler.xml
# .idea/jarRepositories.xml
# .idea/modules.xml
# .idea/*.iml
# .idea/modules
# *.iml
# *.ipr

# CMake
cmake-build-*/

# Mongo Explorer plugin
.idea/**/mongoSettings.xml

# File-based project format
*.iws

# IntelliJ
out/

# mpeltonen/sbt-idea plugin
.idea_modules/

# JIRA plugin
atlassian-ide-plugin.xml

# Cursive Clojure plugin
.idea/replstate.xml

# SonarLint plugin
.idea/sonarlint/

# Crashlytics plugin (for Android Studio and IntelliJ)
com_crashlytics_export_strings.xml
crashlytics.properties
crashlytics-build.properties
fabric.properties

# Editor-based Rest Client
.idea/httpRequests

# Android studio 3.1+ serialized cache file
.idea/caches/build_file_checksums.ser
# ----------------------------------  JetBrains  -----------------------------
#--------------------------------
```



##### 2.2 Terminator

```bash
sudo add-apt-repository ppa:gnome-terminator
sudo apt update
sudo apt install terminator

# 设置默认终端
gsettings set org.gnome.desktop.default-applications.terminal exec /usr/bin/terminator
gsettings set org.gnome.desktop.default-applications.terminal exec-arg "-x"

# 更换回默认终端
# gsettings reset org.gnome.desktop.default-applications.terminal exec
# gsettings reset org.gnome.desktop.default-applications.terminal exec-arg
```



##### 2.3 sublime

```bash
#安装GPG密钥
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
 
#确保apt设置为使用https源
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
 
#我们选择稳定版本
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
 
#更新apt源并安装sublime-text
sudo apt-get update
sudo apt-get install sublime-text


<<EOF
软件汉化
 1. 进入sublime-text 主界面，快捷键  Ctrl + Shift + P  输入 Package Control: install Package 命令， 注意主界面底部的左下脚，会出现动态图标 “ =  = ”，表示后台正在安装插件
 2. 当插件安装成功后，会弹出一个搜索框，如下，这时输入Chinese  关键字，选择下拉框中的ChineseLocalizations 选项，安装中文插件
EOF
```



##### 2.4 idea & pychram

```bash
cd /opt
wget -c -o /dev/null https://download.jetbrains.com/idea/ideaIU-2021.1.3.tar.gz
wget -c -o /dev/null https://download.jetbrains.com/python/pycharm-professional-2021.1.3.tar.gz

tar -zxvf ideaIU-2021.1.3.tar.gz
tar -zxvf pycharm-professional-2021.1.3.tar.gz

# 运行IDEA
./idea-IU-211.7628.21/bin/idea.sh

# 运行pycharm
./pycharm-2021.1.3/bin/pycharm.sh 
```



##### 2.5 sshfs

```bash
# sshfs 是基于ssh的一个工具，用于挂载远端的文件系统到本地. 只要可以用ssh方式访问远端，那么就可以用sshfs 这个工具把有权限的目录挂载到本地来
apt install sshfs

# 将远程主机目录 /home/hxh 挂载到本地 /mnt/hxh/s43
sshfs hxh@43.128.111.99:/home/hxh /mnt/hxh/s43 

# 复制文件
cp /mnt/hxh/s43/a.txt /home/hxh/temp/a.txt

# 查看这个目录是不是挂载点
mountpoint /mnt/hxh/s43

# 取消挂载
umount /mnt/hxh/s43 
```



##### 2.6 安装 stable diffusion

```bash
# 基于docker
# 1.下载三个文件,个人保存的文件
<<EOF
sd-auto.tar.xz
stable-diffusion-webui-docker.tar.xz
docker-compose.override.yaml
EOF

# 2.解压缩文件
xz -d sd-auto.tar.xz
tar -xvJf stable-diffusion-webui-docker.tar.xz

# 3.导入镜像
docker load -i sd-auto.tar

# 4.进入 stable-diffusion-webui-docker目录
cd stable-diffusion-webui-docker
# 启动SD
# 用GPU运算，执行： (注意: 如果自己GPU的显存低于6G需要 实用刚下载的 docker-compose.override.yml 文件,)
docker compose --profile auto up
# 用CPU运算，执行：
docker compose --profile auto-cpu up

# 访问web主页: http://0.0.0.0:7860

#--------------------------------------------------------

# 下载模型  https://civitai.com/
# 将模型拷贝到指定目录下 stable-diffusion/stable-diffusion-webui-docker/data/StableDiffusion
cp ./chilloutmix_v10.ckpt ~/stable-diffusion/stable-diffusion-webui-docker/data/StableDiffusion/chilloutmix_v10.ckpt

# 例如 文生图描述信息可以如下编写
<<EOF
Prompt
----------
sweet1-000050,nfsw,best quality, masterpiece, illustration, an extremely delicate and beautiful, extremely detailed ,CG ,unity ,8k wallpaper, Amazing, finely detail, masterpiece,best quality,official art, extremely detailed CG unity 8k wallpaper, absurdres, incredibly absurdres, huge filesize, ultra-detailed, highres, extremely detailed, beautiful detailed girl, extremely detailed eyes and face, beautiful detailed eyes, light on face,cute, (FASHI-GIRL), (MAKEUP), 1girl,18 years old,(white side ponytail:1.4), blue eyes,(gigantic_breasts:1.8),(breasts_out:1.6), narrow waist,Particularly strong light,(standing model pose:1.8), (sunglasses:1.6),(underboob:1.6),(micro_bikini:1.9),detailed beach,((detailed breasts view)),(blurry_background:1.4),(from below:1.6),ulzzang-6500-v1.1:0.2, <lora:fte_futuanerV1:0.3>, <lora:fashionGirl_v50:0.1>, <lora:dumeizhu_v10:0.1>, <lora:upshirtUnderboob_v10:0.5>

Negative prompt
----------
(worst quality:2), (low quality:2), (normal quality:2), lowres, normal quality, ((monochrome)), ((grayscale)),paintings, sketches,nipples, skin spots, acnes, skin blemishes, bad anatomy,facing away, looking away,tilted head,lowres,bad anatomy,bad hands, text, error, missing fingers,extra digit, fewer digits, blurry,bad feet,cropped,poorly drawn hands,poorly drawn face,mutation,deformed,worst quality,low quality,normal quality,jpeg artifacts,signature,watermark,extra fingers,fewer digits,extra limbs,extra arms,extra legs,malformed limbs,fused fingers,too many fingers,long neck,cross-eyed,mutated hands,polar lowres,bad body,bad proportions,gross,(nipples:1.9)
EOF

```



#### 3.常用脚本

##### 3.1 服务器初始化配置

```bash
# 服务器系统配置初始化脚本

# 1.设置时区同步
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime &>/dev/null

#安装开发工具
yum install Development tools -y 
# 修改系统语言为简体中文
export LC_ALL=zh_CN.UTF-8  
# 修改系统语言为英语
# export LC_ALL=en_US.UTF-8 

# 设置定时任务 每天1点自动同步时间
if ! crontab -l | egrep "ntpdate" &>/dev/null 
then
	(echo "* */1 * * * ntpdate time.windows.com &>/dev/null" ; crontab -l)|crontab
fi


# 2.历史命令显示操作时间、用户
if ! grep HISTTIMEFORMAT /etc/bashrc
then
    echo 'export HISTTIMEFORMAT="%F %T `whoami` "' >> /etc/bashrc
fi


# SSH超时时间
if ! grep "TMOUT=600" /etc/profile &>/dev/null; then
    echo "export TMOUT=600" >> /etc/profile
fi

# 禁止root远程登录
sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
# 禁止空密码用户登录
sed -i 's/#PermitEmptyPasswords no/PermitRootLogin no/' /etc/ssh/sshd_config
# 登录密码错误次数超过3次自动断开连接
sed -i 's/#MaxAuthTries 6/MaxAuthTries 3/' /etc/ssh/sshd_config

# 禁止定时任务发送邮件
sed -i 's/^MAILTO=root/MAILTO=""/' /etc/crontab 


# 设置最大打开文件数
if ! grep "* soft nofile 65535" /etc/security/limits.conf &>/dev/null; then
    cat >> /etc/security/limits.conf << EOF
    * soft nofile 65535
    * hard nofile 65535
EOF
fi

# 减少SWAP使用
echo "0" > /proc/sys/vm/swappiness

# 安装系统性能分析工具及其他
#sudo yum install gcc make autoconf vim sysstat net-tools iostat iftop iotp lrzsz -y

<<EOF
# 逐行读取,将当前行内容赋值给 line
cat ~/temp/a.txt | while read line
do
	echo $line
done
EOF
```



##### 3.2 批量新增用户

```bash
#!/usr/bin/bash

# 批量创建用户的脚本

# 将新增用户名密码保存到这个文件
USER_FILE=user.txt

for user in $@
do
	if  ! id $user &>/dev/null
	then
		# 生成的随机数用MD5加密后生成32位字符串,并截取前8位
		password=$(echo $RANDOM|md5sum|cut -c 1-8) 

		# 创建用户分配家目录和用户组
		mkdir -p /home/$user	
		useradd -d /home/$user -g test $user
		# 修改用户密码 ubuntu系统 
		echo $user:$password|chpasswd  
		if id $user
		then
			echo "$user:$password" >> user.txt
			echo "create $user success"
		else
			echo "create $user fail"
		fi	
	fi

done
exit
```



##### 3.3 查看服务器资源利用率

```bash
# 一键查看服务器资源利用率

# 1.cpu利用率
cpu(){
	cpu_id=$(vmstat | awk '{if(NR==3)print 100-$15"%"}')
	cpu_used=$(vmstat | awk '{if(NR==3)print $1}3')
	cpu_sy=$(vmstat | awk '{if(NR==3)print $14}')
	io_wait=$(vmstat | awk '{if(NR==3)print $16}')

	echo "cpu使用率:$cpu_id,等待磁盘IO响应使用率:$io_wait%'"
}

# 2.内存利用率

memory(){
	total=$( free -m |awk '{if(NR==2)print $2}')
	memory_used=$( free -m |awk '{if(NR==2)print $3}')
	vir=$( free -m |awk '{if(NR==2)print $6}' )
	echo "总内存:${total}M,已用内存:${memory_used}M，缓存:${vir}M"
}


# 3.硬盘利用率
function disk() {
    fs=$(df -h |awk '/^\/dev/{print $1}')
    for p in $fs; do
        mounted=$(df -h |awk '$1=="'$p'"{print $NF}')
        size=$(df -h |awk '$1=="'$p'"{print $2}')
        used=$(df -h |awk '$1=="'$p'"{print $3}')
        used_percent=$(df -h |awk '$1=="'$p'"{print $5}')
        echo "挂载点: $mounted , 总大小: $size , 使用: $used , 使用率: $used_percent"
    done
}


# 4.tcp连接状态

function tcp_status() {
    # summary=$(ss -antp |awk '{status[$1]++}END{for(i in status) printf i":"status[i]" "}')
    # echo "TCP连接状态 - $summary"

    status=$(netstat -antp | awk {'print $6'})

    LISTEN_COUNT=0
    CLOSE_WAIT=0
    SYN_SENT=0
    for sta in $status
    do
    	if [ $sta = LISTEN ]  
    	then
    		let LISTEN_COUNT+=1


    	elif [ $sta = CLOSE_WAIT ] 
    	then
    		let CLOSE_WAIT+=1


    	elif [ $sta = SYN_SENT ]
    	then 
    		let SYN_SENT+=1
    	fi
    done
    echo "tcp状态: LISTEN:$LISTEN_COUNT,CLOSE_WAIT:$CLOSE_WAIT,SYN_SENT:$SYN_SENT"
    
}

cpu
memory
disk
tcp_status
```



##### 3.4 CPU占用率最高的进程

```bash
#!/usr/bin/bash

ps -eo user,pid,pcpu,pmem,args --sort=-pcpu  |head -n 10

ps -eo user,pid,pcpu,pmem,args --sort=-pmem  |head -n 10
```



##### 3.5 查看网卡实时流量

```bash
#!/usr/bin/bash

bash demo.sh enp3s0

# 查看网卡实时流量
while true
do
 	# $0 匹配以 enp3s0开头的每一行, 然后输入第二列
	oldRece=$(awk '$0~/^'$1'/{print $2}' /proc/net/dev)   
	oldTran=$(awk '$0~/^'$1'/{print $(NF-7)}' /proc/net/dev)
	
	# 内置函数 脚本休眠一秒
	sleep 1

	newRece=$(awk '$0~/'$1'/{print $2}' /proc/net/dev)
	newTran=$(awk '$0~/'$1'/{print $(NF-7)}' /proc/net/dev)

	# shell脚本做数学运算格式  $((a-b))
	download=$(( ($newRece - $oldRece)/1024 ))
	upload=$(( ($newTran - $oldTran)/1024 ))
	echo "$1网卡实时下载速度:${download}KB/s,上传速度 ${upload}KB/s"
done
```



##### 3.6 发送邮件

```bash
# 安装 bsd-mailx
sudo apt update && sudo apt install bsd-mailx

# 编辑发送者信息 /etc/mail.rc
#-----------------------------------
set ssl-verify=ignore
set nss-config-dir=/etc/pki/nssdb
set from=linux.hxh@outlook.com  # 发送者邮箱
set smtp=smtp.office365.com  # stmp 服务器地址
set smtp-auth-user=linux.hxh@outlook.com # 发送者建议跟 from相同
set smtp-auth-password="password"
set smtp-auth=login
#-----------------------------------

# 发送邮件
echo "Hello" | mail -s "这里是主题" linux.hxh@gamil.com
mail -s "这里是主题" linux.hxh@gamil.com < article.txt

# 发送附件 -a参数要放在邮件地址的前面  -a 指定文件路径
mail -s "这里是主题" -a ./b.gif linux.hxh@gamil.com < article.txt

#-------------------------------
# 重新配置 bsd-mailx
sudo dpkg-reconfigure postfix

# 查看状态
sudo service postfix status

# 查看邮件日志信息
cat /var/log/mail.log
```



##### 3.7 查看多台服务器磁盘利用率

```bash
#!/usr/bin/bash

# 这是存用多台服务器认证信息的文本路径,格式如下
#--------------------
# 服务器端口默认22  可以不写
192.168.0.1 root
192.168.0.3 alpha
#--------------------
HOST_INTO=$1  # 服务器认证信息的文本路径通过shell传过来 >>  bash demo.sh ./host_info.txt

# 监控多台服务器的磁盘利用率
for IP in $(awk '/^[^#]/{print $1}' $HOST_INTO)  # 匹配以 不是 #开头的行, 方便写注释
do
	echo $IP
	USER=$(awk -v ip=$IP 'ip==$1{print $2}' $HOST_INTO)
	echo $USER

	TEMP_FILE=/home/hxh/temp/disk.info
	ssh $USER@$IP 'df -h' > $TEMP_FILE   # ssh免密登录

	MOUNT_LIST=$(awk '/^\/dev/{print $1}' $TEMP_FILE)
	for MOUNT in $MOUNT_LIST
	do
		echo $MOUNT
		DISK_USED=$(awk -v mount=$MOUNT '$1==mount{print $5}' $TEMP_FILE)
		USED=$(echo $DISK_USED | cut -d "%" -f 1) # 将百分比转成数字方便比较 如：20% 转成 20

		if [ $USED -gt 60  ]
		then
			# 磁盘利用率超过阈值,发送邮件通知管理员及时处理
        	echo "IP:$IP,磁盘挂载路径:$MOUNT,占用率:$USED" |  mail -s "磁盘利用率信息" demo@gamil.com
		fi
		
	done
	
done

```



##### 3.8 检测多个站点是否可访问

```bash

bash demo.sh /home/hxh/shells/domain_info

# 域名文件 domain_info 格式为:
#-------------------------------------------------
http://www.baidu.com
http://www.jd.com
xxxxxxxxxc

#--------------------------------------------------
#!/usr/bin/bash

# 批量检测网站是否可访问

DOMAIN_PATH=$1

for URL in $(awk '{print $1}' $DOMAIN_PATH)
do
	FAIL_COUNT=0
	for((i=0;i<3;i++))
	do
		# -o 表示输入内容到指定文件 /dev/null
		# -s 表示静默输出,不显示任何内容
		# -w "%{http_code}" 拿到请求头的状态码 -w 具体其他参数如下：
		HTTP_CODE=$(curl -o /dev/null -s -w "%{http_code}" $url)
		
		echo $HTTP_CODE | grep ^[2,3] # 判断状态码是否以2,3开头
		STATUS=$?
		if [ $STATUS -eq 0 ]
		then
			break
		else
			let FAIL_COUNT++  # 让FAIL_COUNT变量自增加1 不需要加 $f 符号修饰
		fi
	done

	if [ $FAIL_COUNT -eq 3 ]
	then
		echo "$URL 无法访问,请及时处理" |  mail -s "服务器访问错误" demo@gamil.com
	fi	
done

#---------------------------------------------------------
<<EOF
url_effective 最终获取的url地址，尤其是当你指定给curl的地址存在301跳转，且通过-L继续追踪的情形。

http_code http状态码，如200成功,301转向,404未找到,500服务器错误等。(The numerical response code that was found in the last retrieved 

HTTP(S) or FTP(s) transfer. In 7.18.2 the alias response_code was added to show the same info.)

http_connect The numerical code that was found in the last response (from a proxy) to a curl CONNECT request. (Added in 7.12.4)

time_total 总时间，按秒计。精确到小数点后三位。 （The total time, in seconds, that the full operation lasted. The time will be displayed with millisecond resolution.）

time_namelookup DNS解析时间,从请求开始到DNS解析完毕所用时间。(The time, in seconds, it took from the start until the name resolving was completed.)

time_connect 连接时间,从开始到建立TCP连接完成所用时间,包括前边DNS解析时间，如果需要单纯的得到连接时间，用这个time_connect时间减去前边time_namelookup时间。以下同理，不再赘述。(The time, in seconds, it took from the start until the TCP connect to the remote host (or proxy) was completed.)

time_appconnect 连接建立完成时间，如SSL/SSH等建立连接或者完成三次握手时间。(The time, in seconds, it took from the start until the SSL/SSH/etc connect/handshake to the remote host was completed. (Added in 7.19.0))

time_pretransfer 从开始到准备传输的时间。(The time, in seconds, it took from the start until the file transfer was just about to begin. This includes all pre-transfer commands and negotiations that are specific to the particular protocol(s) involved.)

time_redirect 重定向时间，包括到最后一次传输前的几次重定向的DNS解析，连接，预传输，传输时间。(The time, in seconds, it took for all redirection steps include name lookup, connect, pretransfer and transfer before the final transaction was started. time_redirect shows the complete execution time for multiple redirections. (Added in 7.12.3))

time_starttransfer 开始传输时间。在发出请求之后，Web 服务器返回数据的第一个字节所用的时间(The time, in seconds, it took from the start until the first byte was just about to be transferred. This includes time_pretransfer and also the time the server needed to calculate the result.)

size_download 下载大小。(The total amount of bytes that were downloaded.)

size_upload 上传大小。(The total amount of bytes that were uploaded.)

size_header 下载的header的大小(The total amount of bytes of the downloaded headers.)

size_request 请求的大小。(The total amount of bytes that were sent in the HTTP request.)

speed_download 下载速度，单位-字节每秒。(The average download speed that curl measured for the complete download. Bytes per second.)

speed_upload 上传速度,单位-字节每秒。(The average upload speed that curl measured for the complete upload. Bytes per second.)

content_type 就是content-Type，不用多说了，这是一个访问我博客首页返回的结果示例(text/html; charset=UTF-8)；(The Content-Type of the 

requested document, if there was any.)

num_connects 最近的的一次传输中创建的连接数目。Number of new connects made in the recent transfer. (Added in 7.12.3)

num_redirects 在请求中跳转的次数。Number of redirects that were followed in the request. (Added in 7.12.3)

redirect_url When a HTTP request was made without -L to follow redirects, this variable will show the actual URL a redirect would take you to. (Added in 7.18.2)

ftp_entry_path 当连接到远程的ftp服务器时的初始路径。The initial path libcurl ended up in when logging on to the remote FTP server. (Added in 7.15.4)

ssl_verify_result ssl认证结果，返回0表示认证成功。( The result of the SSL peer certificate verification that was requested. 0 means the verification was successful. (Added in 7.19.0))
EOF
```



##### 3.9在多台机器执行同一命令

```bash
# host-info 文件格式
127.0.0.1 root pwdxxx
#---------------------------------------
#!/usr/bin/bash

COMMAND="df -h"
for IP in $(awk '{print $1}' /home/hxh/temp/host-info)
do
	USERNAME=$(awk -v ip=$IP 'ip==$1{print $2}' /home/hxh/temp/host-info)
	PWD=$(awk -v ip=$IP 'ip==$1{print $3}' /home/hxh/temp/host-info)

	# expect -c 表示在命令行执行指令 
	expect -c "
		spawn ssh $USERNAME@$IP
		expect {
		\"password\" {send \"$PWD\r\"; exp_continue} # 只能用双引号括起来 exp_continue 表示继续匹配,一般用作多次匹配期望值
		\"#\" {send \"$COMMAND\r exit\r\"; exp_continue}
		} "
done


#------------------------------------------

#!/usr/local/bin/expect 
# 使用expect解释器来运行脚本  expect demo

spawn ssh root@127.0.0.1 # 开启一个shell进程
expect "password:" {send "xxxxxx\r"} 
expect "#" {send "df -h\r"} # 因为通过spawn开启了进程,所以可以自动解释 df-h 指令
interact # 交互动作交给用户
```



##### 3.10 部署LNMP

```bash
<<EOF
# 使用网络 LNMP 脚本比较便捷
wget -c http://mirrors.linuxeye.com/oneinstack-full.tar.gz && tar xzf oneinstack-full.tar.gz && ./oneinstack/install.sh --nginx_option 1 --php_option 9 --phpcache_option 1 --db_option 2 --dbinstallmethod 1 --dbrootpwd oneinstack --reboot 
EOF

#!/usr/bin/bash
NGINX_V=1.15.6
PHP_V=5.6.36
TMP_DIR=/tmp

INSTALL_DIR=/usr/local

PWD_C=$PWD

echo
echo -e "\tMenu\n"
echo -e "1. Install Nginx"
echo -e "2. Install PHP"
echo -e "3. Install MySQL"
echo -e "4. Deploy LNMP"
echo -e "9. Quit"

function command_status_check() {
	if [ $? -ne 0 ]; then
		echo $1
		exit
	fi 
}

function install_nginx() {
    cd $TMP_DIR
    yum install -y gcc gcc-c++ make openssl-devel pcre-devel wget
    wget http://nginx.org/download/nginx-${NGINX_V}.tar.gz
    tar zxf nginx-${NGINX_V}.tar.gz
    cd nginx-${NGINX_V}
    ./configure --prefix=$INSTALL_DIR/nginx \
    --with-http_ssl_module \
    --with-http_stub_status_module \
    --with-stream
    command_status_check "Nginx - 平台环境检查失败！"
    make -j 4 
    command_status_check "Nginx - 编译失败！"
    make install
    command_status_check "Nginx - 安装失败！"
    mkdir -p $INSTALL_DIR/nginx/conf/vhost
    alias cp=cp ; cp -rf $PWD_C/nginx.conf $INSTALL_DIR/nginx/conf
    rm -rf $INSTALL_DIR/nginx/html/*
    echo "ok" > $INSTALL_DIR/nginx/html/status.html
    echo '<?php echo "ok"?>' > $INSTALL_DIR/nginx/html/status.php
    $INSTALL_DIR/nginx/sbin/nginx
    command_status_check "Nginx - 启动失败！"
}

function install_php() {
	cd $TMP_DIR
    yum install -y gcc gcc-c++ make gd-devel libxml2-devel \
        libcurl-devel libjpeg-devel libpng-devel openssl-devel \
        libmcrypt-devel libxslt-devel libtidy-devel
    wget http://docs.php.net/distributions/php-${PHP_V}.tar.gz
    tar zxf php-${PHP_V}.tar.gz
    cd php-${PHP_V}
    ./configure --prefix=$INSTALL_DIR/php \
    --with-config-file-path=$INSTALL_DIR/php/etc \
    --enable-fpm --enable-opcache \
    --with-mysql --with-mysqli --with-pdo-mysql \
    --with-openssl --with-zlib --with-curl --with-gd \
    --with-jpeg-dir --with-png-dir --with-freetype-dir \
    --enable-mbstring --enable-hash
    command_status_check "PHP - 平台环境检查失败！"
    make -j 4 
    command_status_check "PHP - 编译失败！"
    make install
    command_status_check "PHP - 安装失败！"
    cp php.ini-production $INSTALL_DIR/php/etc/php.ini
    cp sapi/fpm/php-fpm.conf $INSTALL_DIR/php/etc/php-fpm.conf
    cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
    chmod +x /etc/init.d/php-fpm
    /etc/init.d/php-fpm start
    command_status_check "PHP - 启动失败！"
}

read -p "请输入编号：" number
case $number in
    1)
        install_nginx;;
    2)
        install_php;;
    3)
        install_mysql;;
    4)
        install_nginx
        install_php
        ;;
    9)
        exit;;
esac
```



##### 3.11 检测mysql主从同步状态

```bash
#!/usr/bin/bash

# 监控mysql主从同步服务是否异常
# -e "show slave status\G " 登录mysql服务器的同时执行该指令

# 如果mysql部署在docker上,需要执行以下命令
# docker exec -it mysql01 bash
# ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';

HOST=192.168.3.10
STATUS=$(mysql -P3307 -h$HOST -uroot -proot -e "show slave status\G "|awk '/Slave.*Running:/{print $1$2}')
Slave_IO_Running=$(echo $STATUS |cut -d " " -f 1|cut -d ":" -f 2)
Slave_SQL_Running=$(echo $STATUS |cut -d " " -f 2|cut -d ":" -f 2)

if  (( $Slave_IO_Running != "Yes" || $Slave_IO_Running != "Yes" ))
then
	echo "主从服务器同步出错" | mail -s "$HOST mysql数据同步出错" demo@gamil.com
fi
exit

#--------------------------------------------
# 开启定时任务,定时检测服务状态
crontab -e
#-------------------------------------
*/1 * * * * /home/hxh/shells/10.sh # 每隔一分钟检测一次
```



##### 3. 12 nginx 统计分析

```bash
#!/usr/bin/bash

# 统计每个访问IP的次数
awk '{ips[$1]++}END{for(ip in ips)print ips[ip],ip}' access.log

# 统计每个页面访问的次数
awk '{pages[$7" "$9]++}END{for(page in pages)print pages[page],page}' access.log

# 统计时间段内访问次数最多的IP
# $4<"[28/Apr/2023:10:29:09"&& $4>"[28/Apr/2023:10:21:09"  $4数据类型是date
# sort 结果正序排列  head -10 取前10个
awk '$4<"[28/Apr/2023:10:29:09"&& $4>"[28/Apr/2023:10:21:09" {ips[$1]++}END{for(ip in ips)print ips[ip],ip}' access.log | sort | head -10
```



##### 3. 13 nginx 日志按天切割

```bash
#!/bin/bash

# nginx日志按天切割
LOG_DIR=/usr/local/nginx/logs
YESTERDAY_TIME=$(date -d "yesterday" +%F)
LOG_MONTH_DIR=$LOG_DIR/$(date +"%Y-%m")
LOG_FILE="default.access.log"

if [ -e $LOG_DIR/$LOG_FILE ]
then
    [ ! -d $LOG_MONTH_DIR ] && mkdir -p $LOG_MONTH_DIR
    mv $LOG_DIR/$LOG_FILE  $LOG_MONTH_DIR/${LOG_FILE}_${YESTERDAY_TIME}
fi

kill -USR1 $(cat /var/run/nginx.pid)

# 定时任务 每天晚上 23:59 执行一次脚本
# crontab -e
# * 59 23 * * /home/hxh/shells/demo.sh
```



##### 3.14 自动发布java项目

```bash
#!/usr/bin/bash

# 自动发布java项目
DATE=$(date +%F_%T)

TOMCAT_NAME=$1
TOMCAT_DIR=/usr/local/$TOMCAT_NAME
ROOT=$TOMCAT_DIR/webapps/ROOT

BACKUP_DIR=/data/backup
WORK_DIR=/tmp
PROJECT_NAME=git-demo

cd $WORK_DIR
# 拉取代码
if [ ! -d $PROJECT_NAME ]
then
	# 不存在该项目,直接拉取
	git clone git@gitee.com:hxhabc/git-demo.git
else
	# 存在该项目则需要增量更新
	cd $PROJECT_NAME
	git pull
fi

# 构建maven项目
mvn clean package -Dmaven.test.skip=true
if [ $? == 0 ]
then
	echo "maven 构建项目：$PROJECT_NAME 失败"
	exit 
fi

# 部署

# 找到之前部署的java项目进程PID,并结束进程
TOMCAT_PID=$(ps -ef |grep "$TOMCAT_NAME" |egrep -v "grep --color" | awk 'NR==1{print $2}' )

# -n 判断 PID不为空
[ -n "$TOMCAT_PID" ] && kill -9 $TOMCAT_PID

# -d 判断文件夹存在
[ -d $ROOT ] && mv $ROOT $BACKUP_DIR/${TOMCAT_NAME}_ROOT$DATE

# 解压拉取的最新项目更新到指定目录
unzip $WORK_DIR/$PROJECT_NAME/target/*.war -d $ROOT

# 重启Tomcat服务
$TOMCAT_DIR/bin/startup.sh
```



##### 3.15 自动发布PHP项目

```bash
#!/usr/bin/bash

# 自动发布PHP项目
DATE=$(date +%F_%T)

WWWROOT=/usr/local/nginx/html/$1


BACKUP_DIR=/data/backup
WORK_DIR=/tmp
PROJECT_NAME=php-demo


# 拉取代码
cd $WORK_DIR
if [ ! -d $PROJECT_NAME ]; then
   git git clone git@gitee.com:hxhabc/git-demo.git
   cd $PROJECT_NAME
else
   cd $PROJECT_NAME
   git pull
fi


# 部署
if [ ! -d $WWWROOT ]
then
   mkdir -p $WWWROOT
fi
# rsync 增量更新 --exclude=.git 排除.git文件夹
rsync -avz --exclude=.git $WORK_DIR/$PROJECT_NAME/* $WWWROOT
```



##### 3.16 Dos防范

```bash
#!/usr/bin/bash

# Dos 攻击防范
DATE=$(date +%d/%b/%Y:%H:%M)
LOG_FILE=/usr/local/nginx/logs/demo2.access.log
ABNORMAL_IP=$(tail -n5000 $LOG_FILE |grep $DATE |awk '{a[$1]++}END{for(i in a)if(a[i]>10)print i}')
for IP in $ABNORMAL_IP; do
    if [ $(iptables -vnL |grep -c "$IP") -eq 0 ]; then
        iptables -I INPUT -s $IP -j DROP
        echo "$(date +'%F_%T') $IP" >> /tmp/drop_ip.log
    fi
done
```



##### 3.17 防止ssh暴力破解密码

```bash
<< EOF

lastlog 列出所有用户最近登录的信息
lastlog引用的是/var/log/lastlog文件中的信息，包括login-name、port、last login time

last 列出当前和曾经登入系统的用户信息
它默认读取的是/var/log/wtmp文件的信息。输出的内容包括：用户名、终端位置、登录源信息、开始时间、结束时间、持续时间。注意最后一行输出的是wtmp文件起始记录的时间。当然也可以通过last -f参数指定读取文件，可以是/var/log/btmp、/var/run/utmp

lastb 列出失败尝试的登录信息 
和last命令功能完全相同，只不过它默认读取的是/var/log/btmp文件的信息。当然也可以通过last -f参数指定读取文件，可以是/var/log/btmp、/var/run/utmp

EOF

# 禁止root远程登录
sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
# 禁止空密码用户登录
sed -i 's/#PermitEmptyPasswords no/PermitRootLogin no/' /etc/ssh/sshd_config
# 登录密码错误次数超过3次自动断开连接
sed -i 's/#MaxAuthTries 6/MaxAuthTries 3/' /etc/ssh/sshd_config
# 设置本机可以ping 别的主机，而其他主机不能ping本机
iptables -t filter -I INPUT -p icmp --icmp-type 8 -j REJECT
#-------------------------------------------------------

# fail2ban用来保护ssh的原理非常简单，主要就是通过检测ssh的日志(/var/log/secure)，记录下频繁登录失败的IP，使用iptables禁用掉这个IP即可实现ssh的防暴力破解
# 安装 fail2ban (centos 7)
yum install epel-release
yum install fail2ban

# fail2ban 配置文件目录
ls /etc/fail2ban

# 加载自定义配置
vim /etc/fail2ban/jail.d/sshd.local
#------------------------------------------------
[DEFAULT]
# 以空格分隔的列表，可以是 IP 地址、CIDR 前缀或者 DNS 主机名
# 用于指定哪些地址可以忽略 fail2ban 防御
ignoreip = 127.0.0.1/24

# 客户端主机被禁止的时长（秒）
bantime = 8640000

# 客户端主机被禁止前允许失败的次数
maxretry = 3

# 查找失败次数的时长（秒）
findtime = 600

mta = sendmail

[ssh-iptables]
enabled = true
filter = sshd
action = iptables[name=SSH, port=ssh, protocol=tcp]
sendmail-whois[name=SSH-Fail2ban, dest=linux.hxh@outlook.com, sender=linux.hxh@gmail.com]
# Red Hat 系的发行版
logpath = /var/log/secure
# ssh 服务的最大尝试次数
maxretry = 3
#------------------------------------------------

# 重启 fail2ban 服务
systemctl restart fail2ban && systemctl status fail2ban

# 测试 fail2ban 运行是否正常
# [root@VM-0-4-centos jail.d]# fail2ban-client ping
Server replied: pong

# 设置服务开机自启
systemctl enable fail2ban

# 查看 fail2ban 日志 记录 ip ban|unban信息
tail -f /var/log/fail2ban.log

# 查看 fail2ban 状态
fail2ban-client status
fail2ban-client status ssh-iptables

# 查看iptables的禁用情况
iptables --list -n

# 解禁一个特定IP
fail2ban-client set ssh-iptables unbanip 111.174.36.87
```



##### 3.18 一键更换软件源

```bash
#!/usr/bin/bash

# [ 适用于 ubuntu ]
[ $(id -u) != 0 ] && echo "请切换到root用户" && exit 1

update_software_source(){
# 备份原软件源
cp /etc/apt/sources.list /etc/apt/sources.list.backup

cat > /etc/apt/sources.list << 'EOF'
#------------------------------

# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse

# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse
# deb-src http://security.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse

#------------------------------
EOF

}

query_software_source(){
	cat /etc/apt/sources.list
}

recover_software_source(){
	if [ -e /etc/apt/sources.list.backup ]
	then
		rm /etc/apt/sources.list
		mv /etc/apt/sources.list.backup /etc/apt/sources.list
		
	fi
}


while [ 1 == 1 ]
do
	echo $'\n\n'
	echo "输入:1 查看软件源"
	echo "输入:2 修改软件源"
	echo "输入:3 还原软件源"
	echo "输入:4 退出"

	read -p "输入选项:" number
	case $number in
    1)  query_software_source
    ;;
    2)  update_software_source
    ;;
    3)  recover_software_source
    ;;
    4)  exit 0
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
	esac

done

```



##### 3.19 一键搭建代理服务器

```bash
#!/usr/bin/bash

install_tinyproxy(){

yum list | grep yum
[ $? != 0 ] && echo "请先安装 yum" && exit 1

yum list | grep tinyproxy
[ $? == 0 ] && echo "已经存在tinyproxy,无需重复安装" && exit 1

yum install -y tinyproxy
[ $? != 0 ] && echo "安装 tinyproxy失败" && exit 1

# 修改配置文件,允许所有外部主机访问
cp /etc/tinyproxy/tinyproxy.conf /etc/tinyproxy/tinyproxy.conf.backup
sed -i 's/Allow 127.0.0.1/#Allow 127.0.0.1/' /etc/tinyproxy/tinyproxy.conf

iptables -I INPUT -p tcp --dport 8888 -j ACCEPT
systemctl restart tinyproxy


}

while [ 1 == 1 ]
do
	# 获取公网IP
	IP=$(host myip.opendns.com resolver1.opendns.com | awk '/address / {print $4}')

	[ $(id -u) != 0 ] && echo "请切换到root用户" && exit 1
	echo $'\n\n'
	echo "可以通过其他主机来测试代理服务器是否可用: telnet $IP 8888"
	echo "输入:1 安装 tinyproxy"
	echo "输入:2 查看 tinyproxy 服务状态"
	echo "输入:3 开启 tinyproxy 服务"
	echo "输入:4 停止 tinyproxy 服务"
	echo "输入:5 重启 tinyproxy 服务"
	echo "输入:6 卸载 tinyproxy 服务"
	echo "输入:0 退出"

	read -p "输入选项:" number
	case $number in
    1)  install_tinyproxy
    ;;
    2)  systemctl status tinyproxy.service
    ;;
    3)  systemctl start tinyproxy.service
    ;;
    4)  systemctl stop tinyproxy.service
    ;;
    5)	systemctl restart tinyproxy.service
    ;;
    6)	systemctl stop tinyproxy.service
		yum remove tinyproxy
		echo "卸载 tinyproxy 完成"
    ;;
    0)	exit 0
    ;;
    *)  echo '你没有输入 1 到 6 之间的数字'
    ;;
	esac
done
```



##### 3.20 一键安装开发环境

```bash
#!/usr/bin/bash

# ubuntu 一键安装开发环境
<<EOF
安装之前 确认保软件源是否已经更换

编程环境:
jdk ,maven
python3 ,pip, 虚拟环境
node
-----------------
工具安装:
vim
Terminator
sublime
idea
pycharm
docker
git 修改配置文件
EOF

check_commond_status(){
	if [ $? != 0 ]
	then
		echo $1
	fi
}

install_jdk(){
	java -version &> /dev/null
	[ $? == 0 ] && echo "已经安装jdk" && return

	read -p "请输入本地JDK文件 (如 ：/home/hxh/java11_bin.tar.gz) 的全路径位置: " jdk_path
	if [ ! -e $jdk_path ] 
	then
		echo "文件不存在,请再次确认位置"
		return
	fi
	
	mkdir -p /usr/lib/jdk
	tar -zxvf $jdk_path -C /usr/lib/jdk/
	cd /usr/lib/jdk/
	jdk_file_name=$(ls . | awk '{print $1}')


cat >> /etc/profile << EOF
# set JAVA_ENV
export JAVA_HOME=/usr/lib/jdk/$jdk_file_name
EOF

cat >> /etc/profile << 'EOF'
export CLASSPATH=.:${JAVA_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH
EOF
	
	source /etc/profile
	update-alternatives --install /usr/bin/java  java  /usr/lib/jdk/$jdk_file_name/bin/java 300   
	update-alternatives --install /usr/bin/javac  javac  /usr/lib/jdk/$jdk_file_name/bin/javac 300

	java -version &> /dev/null
	if [ $? == 0 ]
	then
		echo "安装jdk成功" 
	else
		echo "安装jdk失败"
	fi
}

install_maven(){
	mvn -v &> /dev/null
	[ $? == 0 ] && echo "已经安装maven" && return

	read -p "请输入本地maven文件 (如 ：/home/hxh/maven3.8_bin_tar.gz) 的全路径位置: " maven_path
	if [ ! -e $maven_path ] 
	then
		echo "文件不存在,请再次确认位置"
		return
	fi

	mkdir -p /usr/lib/mvn
	tar -zxvf $maven_path -C /usr/lib/mvn/
	cd /usr/lib/mvn/
	mvn_file_name=$(ls . | awk '{print $1}')

cat >> /etc/profile << EOF
#set maven env
export M2_HOME=/usr/lib/mvn/$mvn_file_name
EOF

cat >> /etc/profile << 'EOF'
export CLASSPATH=$CLASSPATH:$M2_HOME/lib
export PATH=$PATH:$M2_HOME/bin
EOF
	
	source /etc/profile && mvn -v &> /dev/null
	if [ $? == 0 ]
	then
		echo "安装maven成功" 
	else
		echo "安装maven失败"
	fi
}	


install_python(){

	# 安装python3
	python3 -V &> /dev/null
	if [ $? != 0 ]
	then
		apt-get install software-properties-common -y
		apt update && apt upgrade
		apt install python3.10

		python3 -V &> /dev/null
		[ $? != 0 ] && echo "安装python失败" && return

		python_path=$(which python3|awk '{print $0}')

cat >> /etc/bash.bashrc <<EOF
alias python=$python_path
EOF
		source /etc/bash.bashrc
		echo "python3 安装成功"
	fi



	# 安装PIP
	pip -V &>/dev/null
	if [ $? != 0 ]
	then
		apt install python3-pip -y
		pip3 install --upgrade pip
		pip -V &> /dev/null
		echo "pip 安装成功"
	fi
	

	# 安装虚拟环境
	pip list |grep virtualenv &>/dev/null
	if [ $? != 0 ]
	then
		pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
		pip install virtualenv
		pip install virtualenvwrapper

		# 虚拟环境存放的目录
		mkdir $HOME/.virtualenvs 
		export WORKON_HOME=$HOME/.virtualenvs
cat >> ~/.bashrc <<EOF
source /usr/local/bin/virtualenvwrapper.sh
EOF
		source ~/.bashrc
		echo "python虚拟环境安装成功"
	fi

}


install_node(){

	node -v &>/dev/null
	if [ $? != 0 ]
	then

		wget -V &>/dev/null
		[ $? != 0 ] && apt install wget -y
		cd /usr/local
		wget -c https://nodejs.org/dist/v18.12.1/node-v18.12.1-linux-x64.tar.xz && xz -d node-v18.12.1-linux-x64.tar.xz
		tar -xvf node-v18.12.1-linux-x64.tar 
		mv node-v18.12.1-linux-x64 node
		


cat >> /etc/profile << 'EOF'
#-------------------------------------
#set node env
export NODE_HOME=/usr/local/node 
export PATH=$NODE_HOME/bin:$PATH
#-------------------------------------
EOF

		source /etc/profile
		ln -s /usr/local/node/bin/node /usr/bin/node
		ln -s /usr/local/node/bin/npm /usr/bin/npm
	fi
	
}

install_vim(){
	vim -h &>/dev/null
	if [ $? != 0 ]
	then
		apt install vim -y
	fi
	echo "vim安装成功"
}


install_terminator(){
	terminator -v &>/dev/null
	if [ $? != 0 ]
	then
		add-apt-repository ppa:gnome-terminator
		apt update
		apt install terminator

		# 设置默认终端
		gsettings set org.gnome.desktop.default-applications.terminal exec /usr/bin/terminator
		gsettings set org.gnome.desktop.default-applications.terminal exec-arg "-x"
	fi
	echo "terminator安装成功"
}



install_sublime(){
	
	wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
	apt-get install apt-transport-https
	echo "deb https://download.sublimetext.com/ apt/stable/" | tee /etc/apt/sources.list.d/sublime-text.list
	apt-get update && apt-get install sublime-text
	if [ $? == 0 ]
	then
		echo "安装sublime成功" && return
	fi
	echo "安装sublime失败,请重试"
}


install_idea(){
	[ -e /opt/idea-IU-211.7628.21 ] && echo "已经安装IDEA2021.1.3,无需重复安装" && return

	cd /opt
	wget -c -o /dev/null https://download.jetbrains.com/idea/ideaIU-2021.1.3.tar.gz
	tar -zxvf ideaIU-2021.1.3.tar.gz

	echo "安装IDEA成功"
	echo "执行 /opt/idea-IU-211.7628.21/bin/idea.sh 打开idea"
}


install_pycharm(){
	[ -e /opt/pycharm-2021.1.3 ] && echo "已经安装pycharm2021.1.3,无需重复安装" && return
	cd /opt
	wget -c -o /dev/null https://download.jetbrains.com/python/pycharm-professional-2021.1.3.tar.gz
	tar -zxvf pycharm-professional-2021.1.3.tar.gz

	echo "安装pycharm成功"
	echo "执行 /opt/pycharm-2021.1.3/bin/pycharm.sh  打开pycharm"
}




install_docker(){

	docker -v &>/dev/null
	[ $? == 0 ] && echo "你已经安装docker,无需重复安装" && return

	cul -V &>/dev/null
	[ $? != 0 ] && apt install curl -y

	curl -fsSL get.docker.com -o get-docker.sh
	sh get-docker.sh --mirror Aliyun

cat > /etc/docker/daemon.json <<EOF
{
"registry-mirrors": [
     "http://hub-mirror.c.163.com",
     "https://docker.mirrors.ustc.edu.cn",
     "https://registry.docker-cn.com"
 ]
}
EOF

	usermod -aG docker $USER
	systemctl restart docker
	docker version

	if [ $? == 0 ]
	then
		echo "docker 安装成功" && return
	fi
	echo "docker 安装失败" 

}


install_git(){
	git version &>/dev/null
	[ $? == 0 ] && echo "你已经安装git,无需重复安装" && return

	apt install git -y

	# 修改git配置文件

cat >> ~/.gitconfig <<'EOF'
[user]
	email = linux.hxh@outlook.com.com
	name = hxh
[core]
	excludesfile = ~/.gitignore # 全局忽略文件所在的路径
EOF

cat >> ~/.gitignore <<'EOF'
#--------------------------------

# ----------------------------------  java  -----------------------------
# Compiled class file
*.class

# Log file
*.log

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files #
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar

# virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
hs_err_pid*
replay_pid*
# ----------------------------------  java  -----------------------------




# ----------------------------------  python  -----------------------------
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
share/python-wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# PyInstaller
#  Usually these files are written by a python script from a template
#  before PyInstaller builds the exe, so as to inject date/other infos into it.
*.manifest
*.spec

# Installer logs
pip-log.txt
pip-delete-this-directory.txt

# Unit test / coverage reports
htmlcov/
.tox/
.nox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover
*.py,cover
.hypothesis/
.pytest_cache/
cover/

# Translations
*.mo
*.pot

# Django stuff:
*.log
local_settings.py
db.sqlite3
db.sqlite3-journal

# Flask stuff:
instance/
.webassets-cache

# Scrapy stuff:
.scrapy

# Sphinx documentation
docs/_build/

# PyBuilder
.pybuilder/
target/

# Jupyter Notebook
.ipynb_checkpoints

# IPython
profile_default/
ipython_config.py

# pyenv
#   For a library or package, you might want to ignore these files since the code is
#   intended to run in multiple environments; otherwise, check them in:
# .python-version

# pipenv
#   According to pypa/pipenv#598, it is recommended to include Pipfile.lock in version control.
#   However, in case of collaboration, if having platform-specific dependencies or dependencies
#   having no cross-platform support, pipenv may install dependencies that don't work, or not
#   install all needed dependencies.
#Pipfile.lock

# poetry
#   Similar to Pipfile.lock, it is generally recommended to include poetry.lock in version control.
#   This is especially recommended for binary packages to ensure reproducibility, and is more
#   commonly ignored for libraries.
#   https://python-poetry.org/docs/basic-usage/#commit-your-poetrylock-file-to-version-control
#poetry.lock

# pdm
#   Similar to Pipfile.lock, it is generally recommended to include pdm.lock in version control.
#pdm.lock
#   pdm stores project-wide configurations in .pdm.toml, but it is recommended to not include it
#   in version control.
#   https://pdm.fming.dev/#use-with-ide
.pdm.toml

# PEP 582; used by e.g. github.com/David-OConnor/pyflow and github.com/pdm-project/pdm
__pypackages__/

# Celery stuff
celerybeat-schedule
celerybeat.pid

# SageMath parsed files
*.sage.py

# Environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

# Spyder project settings
.spyderproject
.spyproject

# Rope project settings
.ropeproject

# mkdocs documentation
/site

# mypy
.mypy_cache/
.dmypy.json
dmypy.json

# Pyre type checker
.pyre/

# pytype static type analyzer
.pytype/

# Cython debug symbols
cython_debug/
# ----------------------------------  python  -----------------------------




# ----------------------------------  JetBrains  -----------------------------
# Covers JetBrains IDEs: IntelliJ, RubyMine, PhpStorm, AppCode, PyCharm, CLion, Android Studio, WebStorm and Rider
# Reference: https://intellij-support.jetbrains.com/hc/en-us/articles/206544839

# User-specific stuff
.idea/**/workspace.xml
.idea/**/tasks.xml
.idea/**/usage.statistics.xml
.idea/**/dictionaries
.idea/**/shelf

# AWS User-specific
.idea/**/aws.xml

# Generated files
.idea/**/contentModel.xml

# Sensitive or high-churn files
.idea/**/dataSources/
.idea/**/dataSources.ids
.idea/**/dataSources.local.xml
.idea/**/sqlDataSources.xml
.idea/**/dynamic.xml
.idea/**/uiDesigner.xml
.idea/**/dbnavigator.xml

# Gradle
.idea/**/gradle.xml
.idea/**/libraries

# Gradle and Maven with auto-import
# When using Gradle or Maven with auto-import, you should exclude module files,
# since they will be recreated, and may cause churn.  Uncomment if using
# auto-import.
# .idea/artifacts
# .idea/compiler.xml
# .idea/jarRepositories.xml
# .idea/modules.xml
# .idea/*.iml
# .idea/modules
# *.iml
# *.ipr

# CMake
cmake-build-*/

# Mongo Explorer plugin
.idea/**/mongoSettings.xml

# File-based project format
*.iws

# IntelliJ
out/

# mpeltonen/sbt-idea plugin
.idea_modules/

# JIRA plugin
atlassian-ide-plugin.xml

# Cursive Clojure plugin
.idea/replstate.xml

# SonarLint plugin
.idea/sonarlint/

# Crashlytics plugin (for Android Studio and IntelliJ)
com_crashlytics_export_strings.xml
crashlytics.properties
crashlytics-build.properties
fabric.properties

# Editor-based Rest Client
.idea/httpRequests

# Android studio 3.1+ serialized cache file
.idea/caches/build_file_checksums.ser
# ----------------------------------  JetBrains  -----------------------------
EOF
	git version
	if [ $? == 0 ]
	then
		echo "git 安装成功" && return
	fi
	echo "git 安装失败" 
}


while [ 1 == 1 ]
do
	[ $(id -u) != 0 ] && echo "请切换到root用户" && exit 1
	echo $'\n\n'
	echo "输入1 安装 jdk"
	echo "输入2 安装 maven"
	echo "输入3 安装 python3,pip,虚拟环境"
	echo "输入4 安装 node"
	echo "============================"
	echo "============================"
	echo "输入5 安装 vim"
	echo "输入6 安装 terminator"
	echo "输入7 安装 sublime"
	echo "输入8 安装 idea2021.3"
	echo "输入9 安装 pycharm2021.3"
	echo "输入10 安装 docker"
	echo "输入11 安装 git"
	echo "输入:0 退出"

	read -p "输入选项:" number
	case $number in
    1)  install_jdk
    ;;
    2)  install_maven
    ;;
    3)  install_python
    ;;
    4)  install_node
    ;;
    5)  install_vim
    ;;
    6)  install__terminator
    ;;
    7)  install__sublime
    ;;
    8)  install_idea
    ;;
    9)  install_pycharm
    ;;
    10)  install_docker
    ;;
    11)  install_git
    ;;
    0)	exit 0
    ;;
    *)  echo '输入有误,请重新输入'
    ;;
	esac
done
```



##### 3.21 ssh开启内网穿透

```bash
# 实现远程公网主机 ,访问本地主机(无公网IP)

<<EOF
-qngfNTR 必备参数
-p 远程主机 ssh连接端口
20000 远程主机开启的端口,远程主机通过这个端口访问本地主机
:22 远程主机监听本地主机22端口
hxh@43.128.111.99 远程主机用户名密码
EOF
# 本地主机运行 
ssh -p 22 -qngfNTR 20000:localhost:22 hxh@43.128.111.99 # 已配置远程免密登录

# -------------------------------

# 远程主机访问本机测试
# 查看 20000端口 状态
ss -ntl |grep 20000
# 远程主机连接到本地主机 root 用户
ssh -p 20000 root@localhost
```



##### 3.22 服务器配置脚本

```bash
```



##### 3.23 一键解压缩脚本

```bash
#!/usr/bin/bash

# 执行出错就退出,不允许使用未定义的变量
set -eu

# 一键解压(t)、压缩文件(u) > bash 27.sh t|u filePath

# 获取需要操作的文件名、参数
operate=$1
filePath=$2

[ ! -e $filePath ] && echo "文件不存在" && exit 1

# 判断解压\压缩文件
if [ $operate == "t" ]; then
    # 判断文件格式 > 使用对应的文件解压命令解压
    extendName=$(echo $filePath | awk -F "." '{print $NF}' | tr [:upper:] [:lower:])
   
    case $extendName in
    rar) unrar t $filePath &>/dev/null && unrar e $filePath &>/dev/null
    ;;
    zip) unzip $filePath
    ;;
    gz) tar -zxvf $filePath
    ;;
    7z) 7z x $filePath
    ;;
    *)  echo '没有对应的解压软件,请先安装' && exit 1
    ;;
    esac


elif [ $operate == "u" ]; then
    read -p "请输入需要压缩的格式:" extendName
    filename=$( echo $(basename $filePath) | awk -F "." '{print $1}')

    case $extendName in
    rar)    rar a ${filename}.rar $filePath &>/dev/null
    ;;
    zip)    zip -rv ${filename}.zip $filePath
    ;;
    gz)     tar -czvf ${filename}.tar.gz $filePath
    ;;
    7z)     7z a ${filename}.7z $filePath
    ;;
    *)      echo '没有对应的解压软件,请先安装' && exit 1
    ;;
    esac

else
    echo "请输入正确参数t\u" && exit 1
fi
```

