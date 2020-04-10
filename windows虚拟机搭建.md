## 安装虚拟机

1、打开vmware主页，创建新的虚拟机，选择自定义高级

2、选择稍后安装操作系统

3、选择Linux, CentOS 64位

4、定义虚拟机的名称，选择虚拟机安装位置（数据存放的磁盘位置）

5、核心，内存使用默认配置

6、网络选择NAT，下三步默认

7、 指定磁盘容量，200G，下一步

8、最后点完成

到这一步相当于有了一台没有装系统的主机，有cpu，硬盘，内存的虚拟主机



### 安装CentOS系统

1、选择刚刚安装的虚拟机，点CD/DVD，使用ISO映像文件，点确定 （这一步相当于买了电脑回来，给光驱里放了一张光盘，剩下的步骤就是开机走向导安装的过程）

2、Install

3、Disc Found，选择skip

4、进入图形安装界面，选择English，选择Basic Storge Device，选择Yes，discard and data

5、Hostname，起一个主机名，node01，选择时区上海

6、给root管理员设置密码，例如hadoop

进入核心环节

7、选择Create Custom Layout  自动创建分区，点下一步

8、给空硬盘分区，/boot 200M， swap  2048M，  / 用掉剩余空间， 重启安装完成



## 配置虚拟机，初始化配置<br/>

1、配置网络<br/>
mac的vmnet8， nat虚拟网卡地址<br/>
https://blog.csdn.net/qq_35583154/article/details/94555128<br/><br/>
执行如下命令进行查找<br/>
find / -name vmnet8<br/>
终于查找到了他的位置<br/><br/>

/Library/Preferences/VMware Fusion/vmnet8<br/>
但这个路径直接复制粘贴是进不去的，可以通过如下命令直接进入<br/><br/>

cd /Library/Preferences/VMware\ Fusion/vmnet8<br/>
进入之后，打开nat.conf文件就可以查看到vmnet8的网关地址<br/><br/>

NAT gateway address<br/>
ip = 172.16.80.2<br/>
netmask = 255.255.255.0<br/><br/>

mac的子网掩码，dns路由器在系统偏好设置网络里<br/><br/>
更详细配置参考https://garryshield.github.io/2016/11/01/mac-vmware-network/ <br/><br/>

```xml
vi /etc/sysconfig/network-scripts/ifcfg-eth0
	注释掉#HWADDR
	ONBOOT=yes
	BOOTPROTO=static
	IPADDR=192.168.9.3
	NETMASK=255.255.255.0
	GATEWAY=192.168.9.2
	DNS1=114.114.114.114
```

service network restart

2、关闭防火墙&&禁用防火墙

```xml
关闭：service iptables stop
禁用：chkconfig iptables off
```

3、关闭 负责安全相关的模块

```xml
vi /etc/selinux/config
SELINUX=disabled
wq
```

4、这一步仅虚拟机测试环境可以设置，公司生产环境绝对不能这样做！

```xml
这一步也是为了clone做准备的，取消mac地址和虚拟机名字绑定。原理跟第一步注释掉#HWADDR是一样的，这里的文件要删掉，这样clone出来的虚拟机就会拿到新的mac地址，当然名字还是eth0
cat /etc/udev/rules.d/70-persistent-net.rules

rm -f etc/udev/rules.d/70-persistent-net.rules
```

5、poweroff



##克隆虚拟机

1、重新设置ip地址

```vi  /etc/sysconfig/network-scripts/ifcfg-eth0```

配置以下参数

```xml
IPADDR=192.168.9.201
:wq 保存
service network restart
```



2、修改主机名字

``` vi /etc/sysconfig/network```

配置以下参数

```xml
HOSTNAME=node0001
:wq
```



3、hosts 添加映射ip

```vi /etc/hosts```

添加以下ip映射

```xml
192.168.9.201 node0001
192.168.9.202 node002
....
```



4、重启虚拟机

poweroof



5、拍摄快照

现在是机器最初始化的状态



##vi 实用快捷键

1、光标插入位置：

```xml
进入编辑模式
	i:从目前光标所在处插入
	l:目前所在行的第一个非空字符处开始插入
	a:从目前光标的下一个字符开始插入
	A:从光标所在行的最后一个字符开始插入
	o:在光标的下一行插入新的一行
	O:在光标的上一行插入新的一行
```



2、复制删除

```xml
在一般模式下
yy: 复制光标所在的一行
p:粘贴
dd: 删除光标所在的一行
x: 向光标后删除一个字符
X: 向光标前删除一个字符
```



3、搜索

```java
/ 从上往下搜索
？ 从下网上搜索
接着敲n 往下搜索， 敲N往上搜索
```

4、翻页

```shell
向下翻页 ctrl+f
向上翻页 ctrl+b
到达底部 G
到达顶部 gg
```

