####***Zookeeper伪集群搭建***####


1. #####下载安装包#####

     [下载地址](https://www.apache.org/dyn/closer.cgi)
     
2. #####提取安装包#####

     ```
     [root@localhost tmp]# tar -zxvf zookeeper-x.x.x.tar.gz
     ```
     
3. #####复制到安装目录#####

     ```
     [root@localhost tmp]# cp zookeeper-x.x.x /usr/local/zookeeper-x.x.x
     ```
     
4. #####创建集群文件夹#####

     转到opt目录：<br/>
     ```
     [root@localhost tmp]# cd /opt
     ```
     创建主目录：<br/>
     ```
     [root@localhost opt]# mkdir zookeeper
     ```
     创建实例目录：<br/>
     ```
     [root@localhost opt]# cd zookeeper
     [root@localhost zookeeper]# mkdir instance_2387
     [root@localhost zookeeper]# mkdir instance_2388
     [root@localhost zookeeper]# mkdir instance_2389
     ```
     
5. #####设置实例配置#####

     转到实例目录：<br/>
     ```
     [root@localhost zookeeper]# cd instance_2387
     ```
     创建数据和日志文件夹：<br/>
     ```
     [root@localhost instance_2387]# mkdir log
     [root@localhost instance_2387]# mkdir data
     ```
     创建对应实例的myid文件：<br/>
     ```
     [root@localhost instance_2387]# cd data
     [root@localhost data]# echo "1" > myid
     ```
     创建对应实例的配置文件：<br/>
     ```
     [root@localhost data]# cd ..
     [root@localhost instance_2387]# touch zoo.cfg
     [root@localhost instance_2387]# vi zoo.cfg
     ```
     添加配置文件内容：<br/>
     ```
     tickTime=2000
     initLimit=10
     syncLimit=5
     clientPort=2387
     autopurge.purgeInterval=24
     autopurge.snapRetainCount=500
     dataDir=/opt/zookeeper/instance_2387/data
     dataLogDir=/opt/zookeeper/instance_2387/log
     server.1=127.0.0.1:2587:3587
     server.2=127.0.0.1:2588:3588
     server.3=127.0.0.1:2589:3589
     ```
     创建其它两个实例的文件夹及配置项目...<br/>
     
     > 备注：
     > &emsp;`clientPort` : 客户端连接zookeeper服务的端口
     > &emsp;`server.[myid]=[ip]:[port1]:[port2]` :
     > &emsp;&emsp;myid 中对应的值，与当前实例 server.[myid] 值一致，不同实例之间的的配置需要进行区分。
     > &emsp;&emsp;port1 : 表示的是服务实例与集群中的Leader服务器交换信息的端口。
     > &emsp;&emsp;port2 : 表示的是万一集群中的Leader服务器宕机了，需要一个端口来重新进行宣讲，选出一个新的Leader。

6. #####设置开机启动#####

     创建启动脚本文件：<br/>
     ```
     [root@localhost /]# touch /etc/init.d/zookeeperd
     ```
     编写启动脚本：<br/>
     ```
     #!/bin/sh
     # chkconfig: 2345 10 90
     # description: Start and Stop zookeeper
     
     export JAVA_HOME=/usr/local/openjdk-11.0.1
     EXEC=/usr/local/zookeeper-3.4.13/bin/zkServer.sh
     INSTANCE_2387=/opt/zookeeper/instance_2387/zoo.cfg
     INSTANCE_2388=/opt/zookeeper/instance_2388/zoo.cfg
     INSTANCE_2389=/opt/zookeeper/instance_2389/zoo.cfg
     
     case "$1" in
         start)
             $EXEC start $INSTANCE_2387
             $EXEC start $INSTANCE_2388
             $EXEC start $INSTANCE_2389
         ;;
         stop)
             $EXEC stop $INSTANCE_2387
             $EXEC stop $INSTANCE_2388
             $EXEC stop $INSTANCE_2389
         ;;
         status)
             $EXEC status $INSTANCE_2387
             $EXEC status $INSTANCE_2388
             $EXEC status $INSTANCE_2389
         ;;
         restart)
             $EXEC restart $INSTANCE_2387
             $EXEC restart $INSTANCE_2388
             $EXEC restart $INSTANCE_2389
         ;;
         *) echo "require start|stop|status|restart" 
         ;;
     esac
     ```
     设置执行权限：<br/>
     ```
     [root@localhost init.d]# chmod a+x zookeeperd
     ```
     
     设置开机启动：<br/>
     ```
     [root@localhost init.d]# chkconfig zookeeperd on
     ```
     
7. #####启动服务：#####

     ```
     [root@localhost init.d]# ./zookeeperd start

     ```
     
8. #####查询集群状态：#####

     ```
     [root@localhost init.d]# ./zookeeperd status

     ```
     