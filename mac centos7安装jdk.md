参考链接：[https://www.cnblogs.com/sxdcgaq8080/p/7492426.html](https://www.cnblogs.com/sxdcgaq8080/p/7492426.html)

1、拖动jdk安装包到SecureCRT，把文件上传到虚拟机服务器

2、删除centos自带的OpenJDK

```java
java -version
  rpm -qa | grep java
  su root
  rpm -e --nodeps + 文件名
  
 检查是否删除成功：java -version
```

3、解压安装

移动jdk mv 到/usr/java

```java
tar -zxvf
```

4、配置jdk全局环境变量

```java
vim /etc/profile
最后一行输入以下内容保存
#java environment
export JAVA_HOME=/usr/java/jdk1.8.0_201
export CLASSPATH=.:${JAVA_HOME}/jre/lib/rt.jar:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
export PATH=$PATH:${JAVA_HOME}/bin
  
使配置生效
  source /etc/profile
```

5、java -version 验证是否成功