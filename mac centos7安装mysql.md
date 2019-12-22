#### CentOS7安装mysql

原文链接：https://blog.csdn.net/weixin_36439837/article/details/88175553

###### Step1 检查是否默认集成了mariadb

```java
rpm -qa | grep mariadb
卸载
rpm -e --nodeps mariadb-libs-5.5.44-1.el7_1.x86_64
```



###### Step2 解压mysql设置权限

```java
1、解压 tar -zxvf 到/usr/local/
2、修改文件名称mv mysql-8.0.4-rc-linux-glibc2.12-x86_64/ mysql
3、为centos添加mysql用户组和mysql用户(-s /bin/false参数指定mysql用户仅拥有所有权，而没有登录权限)
  groupadd mysql
	useradd -r -g mysql -s /bin/false mysql
```



###### Step3 正式安装

```java
1、进入安装mysql软件的目录
  cd /usr/local/mysql
2、修改当前目录拥有者为新建的mysql用户
  chown -R mysql:mysql ./
3、 安装mysql
    ./bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --initialize
    如果出现临时密码，就代表安装成功
4、开启mysql服务
    ./support-files/mysql.server start
如果出现错误，则说明mysql配置文件/etc/my.cnf中的路径不对，修改内容如下，datadir和socket都修改成mysql的安装目录下，增加[client]板块，用于命令行连接mysql数据库。
    [mysqld]

port=3306

datadir=/usr/local/install/mysql /data

socket=/usr/local/install/mysql/mysql.sock

user=mysql

max_connections=151

# Disabling symbolic-links is recommended to prevent assorted security risks

symbolic-links=0

# 设置忽略大小写

lower_case_table_names = 1

# 指定编码

character-set-server=utf8

collation-server=utf8_general_ci

# 开启ip绑定

bind-address = 0.0.0.0

[mysqld_safe]

log-error=/var/log/mysqld.log

pid-file=/var/run/mysqld/mysqld.pid

#指定客户端连接mysql时的socket通信文件路径

[client]

socket=/usr/local/ install/mysql/mysql.sock

default-character-set=utf8

5、重新开启服务
    ./support-files/mysql.server start
6、将mysql进程放入系统进程中
    cp support-files/mysql.server /etc/init.d/mysqld
7、重新启动mysql服务
    service mysqld restart
8、配置mysql环境变量
  	vi /etc/profile
		export PATH=$PATH:/usr/local/mysql/bin
9、使配置生效
      source /etc/profile
10、使用随机密码登录mysql数据库
      mysql -u root -p
      进入mysql操作行，为root用户设置新密码
      	alter user 'root'@'localhost' identified by '123456';
			设置允许远程连接数据库
        use mysql；
        update user set user.Host='%' where user.User='root';
			查看修改后的值：
        select user,host from user;
			刷新权限
        flush privileges;
11、关闭防火墙
  查看firewall服务状态
	systemctl status firewalld
  # 开启
	service firewalld start
	# 重启
	service firewalld restart
	# 关闭
	service firewalld stop
12、navicate测试连接
  如果还是无法远程连接，查看/etc/my.cnf
	找到bind-address = 127.0.0.1这一行
	改为bind-address = 0.0.0.0即可
13、 设置为自启动
  cp /usr/local/mysql/support-files/mysql.server 			   /etc/init.d/mysql   将服务文件拷贝到init.d下，并重命名为mysql
chmod +x /etc/init.d/mysql    赋予可执行权限
chkconfig --add mysql        添加服务
chkconfig --list             显示服务列表
如果看到mysql的服务，并且3,4,5都是on的话则成功，如果是off，则键入
netstat -na | grep 3306，如果看到有监听说明服务启动了
```







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





