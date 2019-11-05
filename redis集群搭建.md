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



## 开始搭建redis集群

3主3备，最少的redis集群要求数

#### 一、创建文件夹

我们计划在集群中设置redis得端口号为9001~9006，端口号即集群中各实例文件夹。数据存放在端口号/data文件夹中

```shell
mkdir /usr/local/redis-cluster
cd redis-cluster/
mkdir -p 9001/data ... 9006/data
```



#### 二、复制执行脚本

在/usr/local/redis-cluster下创建bin文件夹，用来存放集群运行脚本，并把装好的redis的src下的运行脚本拷贝过来

```shell
mkdir redis-cluster/bin
cd /usr/local/redis/src

cp mkreleasehdr.sh redis-benchmark redis-check-aof redis-check-dump redis-cli redis-server redis-trib.rb /usr/local/redis-cluster/bin
```



#### 三、复制一个新redis实例

从已经装好的redis实例中复制一个 新的实例到9001文件夹，并修改redis.conf配置

```shell
cp /usr/local/redis/* /usr/local/redis-cluster/9001
```

```shell
修改redis.conf, 注释不要复制进去
port 9001 #每个节点的端口号
daemonize yes #开机启动，守护进程
bind 192.168.9.4 #绑定当前机器ip
dir /usr/local/redis-cluster/9001/data #数据存放位置
pidfile /var/run/redis_9001.pid #pid，守护进程启动，默认pid要保存的文件夹位置
cluster-enabled yes #启动集群模式
cluster-config-file nodes9001.conf #9001和port要对应
cluster-node-timeout 15000 #节点超时时间15秒
appendonly yes #指定每次更新操作后进行日志记录，如果不开启可能会在断电时导致数据丢失一部分
```



#### 四、再复制出5个新实例

把9001实例再复制5份，把redis.conf中端口号相关的信息修改

```shell
\cp -rf /usr/local/redis-cluster/9001/* /usr/local/redis-cluster/9002
\cp -rf /usr/local/redis-cluster/9001/* /usr/local/redis-cluster/9003
\cp -rf /usr/local/redis-cluster/9001/* /usr/local/redis-cluster/9004
\cp -rf /usr/local/redis-cluster/9001/* /usr/local/redis-cluster/9005
\cp -rf /usr/local/redis-cluster/9001/* /usr/local/redis-cluster/9006

\cp -rf 命令是不使用别名来复制，因为 cp 其实是别名 cp -i，操作时会有交互式确认，比较烦人。
```



#### 五、修改9002~9006的redis.conf

```shell
全局替换命令
vi redis.conf
:%s/9001/9002/g

其实就是替换了下面4行
port 9002
dir /usr/local/redis-cluster/9002/data/
cluster-config-file nodes-9002.conf
pidfile /var/run/redis_9002.pid

到此完成基本的环境
```



#### 六、启动所有节点

```shell
/usr/local/bin/redis-server /usr/local/redis-cluster/9001/redis.conf 
/usr/local/bin/redis-server /usr/local/redis-cluster/9002/redis.conf 
/usr/local/bin/redis-server /usr/local/redis-cluster/9003/redis.conf 
/usr/local/bin/redis-server /usr/local/redis-cluster/9004/redis.conf 
/usr/local/bin/redis-server /usr/local/redis-cluster/9005/redis.conf 
/usr/local/bin/redis-server /usr/local/redis-cluster/9006/redis.conf

ps -el | grep redis 查看进程
```



#### 七、测试

```shell
/usr/local/redis-cluster/bin/redis-cli -h 192.168.9.4 -p 9001

set name mafly

报错(error) CLUSTERDOWN Hash slot not served
6个节点并不在一个集群中，互相不能发现，而且没有可存储的位置，就是所谓的slot（槽）
```



#### 八、安装集群所需的软件

```shell
redis集群需要rudy命令，linux需要安装rudy和相关接口
报错
yum install ruby
yum install rubygems
gem install redis 

```



#### 九、真正的创建集群的一步

```shell
/usr/local/redis-cluster/bin/redis-trib.rb create --replicas 1 192.168.9.4:9001 192.168.9.4:9002 192.168.9.4:9003 192.168.9.4:9004 192.168.9.4:9005 192.168.9.4:9006

简单解释一下这个命令：调用 ruby 命令来进行创建集群，--replicas 1 表示主从复制比例为 1:1，即一个主节点对应一个从节点；然后，默认给我们分配好了每个主节点和对应从节点服务，以及 solt 的大小，因为在 Redis 集群中有且仅有 16383 个 solt ，默认情况会给我们平均分配，当然你可以指定，后续的增减节点也可以重新分配。

M: 10222dee93f6a1700ede9f5424fccd6be0b2fb73 为主节点Id

S: 9ce697e49f47fec47b3dc290042f3cc141ce5aeb 192.168.119.131:9004 replicates 10222dee93f6a1700ede9f5424fccd6be0b2fb73 从节点下对应主节点Id

目前来看，9001-9003 为主节点，9004-9006 为从节点，并向你确认是否同意这么配置。输入 yes 后，会开始集群创建。
```



#### 十、集群测试

```shell
/usr/local/redis-cluster/bin/redis-cli -c -h 192.168.9.4 -p 9001
cluster info
cluster nodes

通过命令，可以详细的看出集群信息和各个节点状态，主从信息以及连接数、槽信息等。这么看到，我们已经真的把 Redis 集群搭建部署成功啦！

设置一个 redis01：
你会发现，当我们 set name redis01 时，出现了 Redirected to slot 信息并自动连接到了9002节点。这也是集群的一个数据分配特性，这里不详细说了。
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

4、翻页

```shell
向下翻页 ctrl+f
向上翻页 ctrl+b
到达底部 G
到达顶部 gg
```

