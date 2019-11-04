## 下载安装redis

#### 一、下载解压redis

<https://www.jianshu.com/p/7ee38c05996c> 

- wget http://download.redis.io/releases/redis-3.2.5.tar.gz 
- tar xzf redis-3.2.5.tar.gz 
- mv redis-3.2.5 /usr/local/redis 



#### 二、进入目录安装

- cd /usr/local/redis
- make 
- make install

安装报错

redis 安装 make: *** [install] Error 2

然后make安装

执行make install ，完成就会在/usr/local/bin目录看到redis-cli和redis-server等可执行脚本



#### 三、配置redis.conf

- cd ../redis

- vim redis.conf

  ```java
  添加
  bind 127.0.0.1 当前机器ip如：
  bind 127.0.0.1 192.168.9.4
  
  修改后台启动redis
  daemonize yes
  ```



#### 4、启动

```java
cd /usr/local/bin
redis-server /usr/local/redis/redis.conf
netstat -anp | grep 6379
```



#### 5、测试连接

```java
在bin目录下
1. redis-cli #连接redis, 默认是本机的
2. keys * #查看现在所有的key
3. set name code00000001 #设置一个key为name,value为code00000001的缓存对象
4. get name #获取key为name的缓存
```



#### 6、关闭redis

```java
退出：exit
关闭：redis-cli shutdown
netstat -anp | grep 6379
ps -ef | grep redis-cli
```







## vi 实用快捷键

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

