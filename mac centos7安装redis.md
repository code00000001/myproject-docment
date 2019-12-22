参考链接：https://blog.csdn.net/qq_33417321/article/details/88924934

一、官网下载tar.gz包，解压到 /usr/local

二、进入redis-5.0.3目录里，执行编译命令

​	make

三、编译完成之后，将redis安装到指定目录

​	make PREFIX=/usr/local/redis install

四、此时/usr/local/redis下生成了一个bin目录，至此安装完成；

五、启动

5.1将redis-5.0.3目录下的redis.conf文件复制到 /usr/local/redis/bin 下

​	cp redis.conf /usr/local/redis/bin/

5.2修改redis.conf 设置为后台启动，将daemonize no改为daemonize yes即可(redis.conf文件内容较多，全局搜索：/daemonize 然后回车，再不断敲N即可)

5.3允许远程连接Redis，redis 默认只允许自己的电脑（127.0.0.1）连接。如果想要其他电脑进行远程连接，将配置文件 redis.conf 中的 bind 127.0.0.1 注释掉(之前没注释，需要改为将其注释掉，默认只能连接本地)。

5.4找到配置文件redis.conf中protected mode，默认protected mode yes，需要将其改为protected mode no

5.5启动Redis：进入/usr/local/redis/bin目录

./redis-server redis.conf

5.6 ps -ef | grep -i redis 看到进程

六、客户端连接测试通过