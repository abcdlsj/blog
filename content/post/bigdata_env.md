+++
title = "大数据环境搭建示例"
date = 2019-11-13
lastmod = 2020-03-22T12:00:04+08:00
tags = ["BigData"]
categories = ["learn"]
draft = false

toc = true

+++

> 上学期参加了联想杯大数据比赛的校内选拔，“领阵磨枪”的我竟然拿了第二（选拔三人），开学这几天基本上就是配置环境。感觉没啥意思，应用层面又不会，初赛我们队因为队友都有事，所以就花了半天。。最后果然没进。

<!--more-->


## 虚拟机安装配置 {#虚拟机安装配置}

比较简单，不说了，真机也行，但是还是虚拟机好，耐操，配好一台直接复制几台就行了

这里强烈推荐使用端口转发，连接虚拟机极其方便，我开始弄网络还弄了半天。


## hadoop伪分布式搭建 {#hadoop伪分布式搭建}


### centos 7 关闭防火墙 {#centos-7-关闭防火墙}

查看是否开启

```shell
sudo firewall-cmd --state
关闭
systemctl stop firewalld
sudo systemctl stop firewalld.service
开机自动关闭
sudo systemctl disable firewalld.service
```


### 安装jdk&&配置环境 {#安装jdk-and-and-配置环境}

```shell
sudo vim /etc/profile
添加
export JAVA_HOME=/home/abcdlsj/jdk/
export PATH=$PATH:$JAVA_HOME/bin
source 此文件
```


### 修改主机名&&host {#修改主机名-and-and-host}

```shell
sudo vim /etc/hosts
sudo vim /etc/hostname
```

`注意此处有坑` 主机名不能有下划线


### hadoop下载&&解压 {#hadoop下载-and-and-解压}


### 配置 {#配置}

```shell
vim hadoop-env.sh
```

修改JAVA\_HOME

```xml
vim core-site.xml

<configuration>
<property>
<name>fs.defaultFS</name>
<value>hdfs://172.16.17.134:9000</value>
</property>
<property>
<name>hadoop.tmp.dir</name>
<value>/home/abcdlsj/hadoop/tmp/</value>
</property>
</configuration>

vim hdfs-site.xml

<configuration>
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
</configuration>

vim mapred-site.xml(默认不是这个记得 mv template)

<configuration>
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>
</configuration>

vim yarn-site.xml

<configuration>
<property>
<name>yarn.resourcemanager.hostname</name>
<value>hadoop</value>
</property>
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
</configuration>
```


### 配置环境PATH {#配置环境path}

```shell
sudo vim /etc/profile

export HADOOP_HOME=/home/abcdlsj/hadoop/
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

source /etc/profile
```


### ssh无密码登录 {#ssh无密码登录}

```shell
ssh-keygen -t rsa

cat id_rsa.pub >> authorized_keys

ssh hadoop
```


### 启动 {#启动}

```shell
hadoop namenode -format

start-dfs.sh
start-yarn.sh

jps 查看是否运行成功

6449 NodeManager
6488 Jps
6009 NameNode
6345 ResourceManager
5693 SecondaryNameNode
6109 DataNode
```


### 固定虚拟机ip地址 {#固定虚拟机ip地址}

```shell
ip addr

ls /etc/sysconfig/network-scripts/

sudo vim /etc/sysconfig/network-scripts/ifcfg-ens33
添加：
IPADDR0=172.16.17.134
PREFIX0=24
GATEWAY0=172.16.17.1
DNS1=172.16.17.1

service network restart
```


## centos mysql 安装 {#centos-mysql-安装}

```shell
cd /usr/local/src/
wget http://repo.mysql.com/mysql57-community-release-el7-8.noarch.rpm
rpm -ivh mysql57-community-release-el7-8.noarch.rpm
yum -y install mysql-server
grep "password" /var/log/mysqld.log
mysql -u root -p
alter user 'root'@'localhost' identified by 'Lisongjian1@';
```


## hive 搭建 {#hive-搭建}


### 修改 /etc/profile {#修改-etc-profile}

```shell
export HIVE_HOME=/home/abcdlsj/hive/
export PATH=${HIVE_HOME}/bin:$PATH

source
```


### 生成 hive-site.xml 和文件夹 {#生成-hive-site-dot-xml-和文件夹}

```shell
cd /home/abcdlsj/hive/conf
cp hive-default.xml.template hive-site.xml
```

```nil
hadoop fs -mkdir -p hive/warehouse
hadoop fs -mkdir -p hive/tmp
hadoop fs -mkdir -p hive/log

hadoop fs -chmod -R 777 hive/warehouse
hadoop fs -chmod -R 777 hive/tmp
hadoop fs -chmod -R 777 hive/log

mkdir /home/abcdlsj/hive/tmp
```


### hive-env.sh {#hive-env-dot-sh}

```shell
cp hive-env.sh.template hive-env.sh
vi hive-env.sh

HADOOP_HOME=/home/abcdlsj/hadoop/
export HIVE_CONF_DIR=/home/abcdlsj/hive/conf
export HIVE_AUX_JARS_PATH=/home/abcdlsj/hive/lib
```


### hive-site.xml {#hive-site-dot-xml}

```shell
把{system:java.io.tmpdir} 改成 /home/abcdlsj/hive/tmp
把 {system:user.name} 改成 {user.name}

hive.exec.scratchdir
hive.querylog.location
javax.jdo.option.connectionURL
javax.jdo.option.ConnectionDriverName
javax.jdo.option.ConnectionUserName
javax.jdo.option.ConnectionPassword
```


### 下载 Mysql 驱动 {#下载-mysql-驱动}


#### 下载 {#下载}

```shell
cd /home/abcdlsj/hive/lib
wget http://central.maven.org/maven2/mysql/mysql-connector-java/5.1.38/mysql-connector-java-5.1.38.jar
```


#### 初始化 {#初始化}

初始化 mysql(hive/bin文件夹)

```nil
./schematool -initSchema -dbType mysql
```

如果显示 没有权限连接

```shell
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'Lisongjian1@' WITH GRANT OPTION;
```

如果显示 useSSL warning 修改：

```shell
jdbc:mysql://localhost:3306/hive?useSSL=false
```

就可以了


## spark 搭建 {#spark-搭建}


### scala 搭建 {#scala-搭建}

下载解压安装包 然后 配置 PATH 就行了


### spark 配置 {#spark-配置}

vim spark/conf/spark.env.sh

```shell
export JAVA_HOME=/home/abcdlsj/jdk
export SCALA_HOME=/home/abcdlsj/scala
export HADOOP_HOME=/home/abcdlsj/hadoop
export SPARK_MASTER_IP=192.168.236.135
export STANDALONE_SPARK_MASTER_HOST=hadoop
export SPARK_LAUNCH_WITH_SCALA=0
export SPARK_LIBRARY_PATH=${SPARK_HOME}/lib
export SCALA_LIBRARY_PATH=${SPARK_HOME}/lib
export SPARK_MASTER_WEBUI_PORT=18080
export HADOOP_CONF_DIR=/home/abcdlsj/hadoop/etc/hadoop
```
