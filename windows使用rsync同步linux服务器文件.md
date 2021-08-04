### windows使用rsync同步linux服务器文件

##### 1.Linux服务器安装rsync；

有yum的可以使用yum命令安装

```
yum -y install rsync
```

接下来就是配置rsync：进入/ect目录下找到rsyncd.conf文件，没有的话手动创建一个：

```
vim rsyncd.conf
```

然后进行配置：

```
# /etc/rsyncd: configuration file for rsync daemon mode
# See rsyncd.conf man page for more options.
# configuration example:
#注：服务器端传输文件时，要发哪个用户和用户组来执行，默认是nobody。 如果用nobody 用户和用户组，可能遇到权限问题，有些文件从服务器上拉不下来。所以我就偷懒，为了方便，用了root 。 
pid file = /var/run/rsyncd.pid
# 端口号
port = 873
# 主机地址
address = 172.x.x.x
uid = root
gid = root
# 忽略IO错误 
ignore errors
#允许进行rsync的客户端网段或IP 
host allow = x.x.x.x/x.x.x.x
# 认证用户是root,是必须在服务器上存在的用户
#auth users = root
# 密码存在哪个文件
secrets file = /etc/rsyncd.secrets
# use chroot = yes
# 最大连接数
max connections = 5
# exclude = lost+found/
# transfer logging = yes
# timeout = 900
# ignore nonreadable = yes
# dont compress   = *.gz *.tgz *.zip *.z *.Z *.rpm *.deb *.bz2
# 认证的模块名，在client端需要指定
[syncdata]
# 指定文件目录所在位置
path = /home/...
# 是否只读
read only = no
```

接下来在etc下创建rsyncd.secrets文件，内容为密码：

```
root:123456
```

将该文件赋予600权限：

```
chmod 600 /etc/rsyncd.secrets
```

最后启动rsync服务：

```
/usr/bin/rsync --daemon --config=/etc/rsyncd.conf
```

以下命令可以查看是否在运行：

```
ps -ef | grep 'rsync'
```

完成后服务端配置完成了；

##### 2.windows客户端

官网的要收费，可以使用以前的旧版本

百度网盘链接: https://pan.baidu.com/s/1dbq8ROC9skrIOytxpwao-Q 提取码: p97g 

将文件下载解压后，直接进行安装，建议不要安装到C盘，用户名和密码配置一个自己能记住的简单的。

安装完后在bin目录下，创建一个fileRsync.cmd文件，如下：

![image-20210804144334410](C:\Users\VDI\AppData\Roaming\Typora\typora-user-images\image-20210804144334410.png)

内容为本地同步服务器内容命令：

```
rsync -auv --password-file=D:/cwRsync/ICW/rsyncd.secrets root@x.x.x.x::syncdata ./syncdata
```

服务器同步本地命令为：

```
rsync -avz --password-file=D:/cwRsync/ICW/rsyncd.secrets ./syncdata/333.html root@x.x.x.x::syncdata
```

注：password-file后面是客户端，需要提供服务器端的密码文件；root代表服务器端用户名；@后面是服务器端的ip，固定不变的；syncdata代表模块名，对应服务器端配置文件里面的参数；./beifendir代表在客户端需要将备分文件存放在哪里，必须使用相对路径，绝对路径会报错。

然后在bin目录的父级目录创建密码文件rsyncd.secrets:

![image-20210804144754212](C:\Users\VDI\AppData\Roaming\Typora\typora-user-images\image-20210804144754212.png)

内容为服务器密码文件的密码

```
123456
```

之后可以执行fileRsync.cmd文件，看能否成功获取到服务器文件。

也可以配置环境变量在全局使用rsync命令：在path变量中新增rsync地址：

![image-20210804145155412](C:\Users\VDI\AppData\Roaming\Typora\typora-user-images\image-20210804145155412.png)

就可在全局使用rsync命令。



###### rsync的相关命参数

-v,--verbose //详细模式输出；

-a,--archive //归档模式，表示以递归的方式传输文件，并保持所有文件属性不变，相当于使用了组合参数-rlptgoD;

-r, --recursive //对子目录以递归模式处理;

-l, --links//保留软链结;

-p, --perms //保持文件权限;

-t, --times//保持文件时间信息;

-g, --group //保持文件属组信息;

-o, --owner //保持文件属主信息;

-D, --devices //保持设备文件信息;

-H, --hard-links //保留硬链结;

-S, --sparse //对稀疏文件进行特殊处理以节省DST的空间;

--delete //删除那些DST中SRC没有的文件;

-z, --compress //对备份的文件在传输时进行压缩处理；

###### rsync六种不同的工作模式：

1.拷贝本地文件，将/home/coremail目录下的文件拷贝到/cmbak目录下

$ rsync -avSH /home/coremail/ /cmbak/

2.拷贝本地机器的内容到远程机器

$ rsync -av /home/coremail/ 192.168.11.12:/home/coremail/

3.拷贝远程机器的内容到本地机器

$ rsync -av 192.168.11.11:/home/coremail/ /home/coremail/

4.拷贝远程rsync服务器(daemon形式运行rsync)的文件到本地机

$ rsync -av root@172.16.78.192::www /databack

5.拷贝本地机器文件到远程rsync服务器(daemon形式运行rsync)中。当DST路径信息包含”::”分隔符时启动该模式

$ rsync -av /databack root@172.16.78.192::www

6.显示远程机的文件列表。这类似于rsync传输，不过只要在命令中省略掉本地机信息即可

$ rsync -v rsync://192.168.11.11/data