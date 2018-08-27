####***Redis集群搭建***####


1. #####下载安装包#####
     [下载地址](https://redis.io/download)
2. #####提取安装包#####
     ```
     [root@localhost tmp]# tar -zxvf redis.x.x.x.tar.gz
     ```
3. #####编译并安装#####
     创建安装目录：<br/>
     ```
     [root@localhost tmp]# mkdir /usr/local/redis.x.x.x
     ```
     转到提取目录：
     ```
     [root@localhost redis.x.x.x]# make
     [root@localhost redis.x.x.x]# make PERFIX=/usr/local/redis.x.x.x/bin install
     ```
     > 注： `PERFIX` 指定要安装到的目录，不指定则默认安装到 `/usr/local/bin`
4. #####创建集群文件夹#####
     转到安装目录：
     ```
     [root@localhost redis.x.x.x]# cd /usr/local/redis.x.x.x
     ```
     创建 9001~9006 集群文件夹：
     ```
     [root@localhost redis.x.x.x]# mkdir -p 9001/data 9002/data 9003/data 9004/data 9005/data 9006/data
     ```
5. #####复制Redis配置文件#####
     把提取的安装包文件夹中的 `redis.conf` 配置文件复制到刚才创建的集群文件夹中：
     ```
     [root@localhost redis.x.x.x]# cp /tmp/redis.x.x.x/redis.conf /usr/local/redis.x.x.x/9001
     ```
6. #####修改Redis配置文件#####
     转到集群文件夹：
     ```
     [root@localhost redis.x.x.x]# cd 9001
     ```
     编辑配置文件：
     ```
     [root@localhost 9001]# vi redis.conf
     ```
     修改配置文件以下配置项：
     ```
     #本节点的端口号
     port 9001
     #后台运行
     daemonize yes
     #绑定本机 IP
     bind x.x.x.x
     #数据文件存放位置
     dir /usr/local/redis.x.x.x/9001/data/
     #9001对应本节点port
     pidfile /var/run/redis-9001.pid
     #启动集群模式
     cluster-enabled yes
     #9001对应本节点port
     cluster-config-file nodes-9001.conf
     #节点超时多久则认为它宕机了
     cluster-node-timeout 15000
     #当其中一个节点（包括主从）都不可用是集群是否不可用，默认是yes集群不可用
     cluster-require-full-coverage no
     appendonly yes
     ```
7. #####复制修改后的配置文件到其它集群文件夹#####
     ```
     [root@localhost 9001]# cp redis.conf /usr/local/redis.x.x.x/9002
     ...
     [root@localhost 9001]# cp redis.conf /usr/local/redis.x.x.x/9006
     ```
     修改配置文件，把其中与端口号相关的配置项修改为当前节点的端口号<br/>
     > 注：包括以下4项配置：
     > ```
     > port
     > dir
     > pidfile
     > clustr-conf-file
     > ```
8. #####启动并检测各个节点#####
     ```
     [root@localhost /]# redis-server /usr/local/redis.x.x.x/9001/redis.conf
     ...
     [root@localhost /]# redis-server /usr/local/redis.x.x.x/9006/redis.conf
     ```
     检测各个节点是否已启动：
     ```
     [root@localhost /]# ps -el | grep redis
     ```
     ```
     5 S     0   401     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     5 S     0   462     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     5 S     0   485     1  0  80   0 - 36489 ep_pol ?        00:00:10 redis-server
     5 S     0   523     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     5 S     0   615     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     5 S     0  1233     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     ```
9. #####安装创建集群所需软件#####
     查询系统ruby版本：
     ```
     [root@localhost /]# ruby -v
     ```
     如果没有安装或者ruby版本小于2.2.2，安装新版本：
     ```
     [root@localhost /]# gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
     [root@localhost /]# curl -sSL https://get.rvm.io | bash -s stable
     [root@localhost /]# source /usr/local/rvm/scripts/rvm
     ```
     查询当前ruby可用版本：
     ```
     [root@localhost /]# rvm list known
     ```
     选择其中一个大于等于2.2.2的版本：
     ```
     [root@localhost /]# rvm install 2.5.1
     ```
     使用安装的新版本：
     ```
     [root@localhost /]# rvm use 2.5.1 --default
     ```
     安装其它依赖包：
     ```
     [root@localhost /]# yum install rubygems
     [root@localhost /]# gem install redis
     ```
10. #####创建redis集群#####
     ```
     [root@localhost /]# /usr/local/redis-4.0.10/bin/redis-trib.rb create --replicas 1 x.x.x.x:9001 x.x.x.x:9002 x.x.x.x:9003 x.x.x.x:9004 x.x.x.x:9005 x.x.x.x:9006
     ```
     > 备注：x.x.x.x 为节点所绑定的IP地址
11. #####查看集群状态#####
     ```
     [root@localhost /]# redis-cli -c -h x.x.x.x -p 9001
     x.x.x.x:9001>cluster info
     cluster_state:ok
     cluster_slots_assigned:16384
     cluster_slots_ok:16384
     cluster_slots_pfail:0
     cluster_slots_fail:0
     cluster_known_nodes:6
     cluster_size:3
     cluster_current_epoch:6
     cluster_my_epoch:1
     cluster_stats_messages_ping_sent:3959
     cluster_stats_messages_pong_sent:1580
     cluster_stats_messages_fail_sent:6
     cluster_stats_messages_sent:5545
     cluster_stats_messages_ping_received:1580
     cluster_stats_messages_pong_received:1560
     cluster_stats_messages_fail_received:3
     cluster_stats_messages_received:3143
     x.x.x.x:9001>cluster nodes
     253bfb35a6308f2df66180a1a60e0146a8eb6175 x.x.x.x:9001@19001 myself,master - 0 1532339047000 1 connected 0-5460
     1157f4871390aff7e4c57afa73c9cd4a74d94ec7 x.x.x.x:9002@19002 master - 0 1532339047000 2 connected 5461-10922
     dabbebaf4f1c9a8ffe7dbeaa26df6ceca41c6eeb x.x.x.x:9003@19003 master - 0 1532339048000 3 connected 10923-16383
     929e2eead64e8f7dd1cc22307e0217a8faa071dd x.x.x.x:9005@19005 slave 1157f4871390aff7e4c57afa73c9cd4a74d94ec7 0 1532339045000 5 connected
     02a47cf383c63ca3a5f92e79bf6dead060e17382 x.x.x.x:9006@19006 slave dabbebaf4f1c9a8ffe7dbeaa26df6ceca41c6eeb 0 1532339047000 6 connected
     09168b94744b9365929ddd1b9d2c6de49faffcd1 x.x.x.x:9004@19004 slave 253bfb35a6308f2df66180a1a60e0146a8eb6175 0 1532339048211 4 connected
     ```
     > 备注：x.x.x.x 为节点所绑定的IP地址
12. #####设置开机启动#####
     创建启动脚本文件：<br/>
     ```
     [root@localhost /]# touch /etc/init.d/redisd
     ```
     编写启动脚本：<br/>
     ```
     #!/bin/sh
     # chkconfig: 2345 10 90  
     # description: Start and Stop redis   
     
     EXEC=/usr/local/redis-x.x.x/bin/redis-server
     CLIEXEC=/usr/local/redis-x.x.x/bin/redis-cli
     
     PIDFILE_9001=/var/run/redis-9001.pid
     CONF_9001="/usr/local/redis-x.x.x/9001/redis.conf"
     
     PIDFILE_9002=/var/run/redis-9002.pid
     CONF_9002="/usr/local/redis-x.x.x/9002/redis.conf"
     
     PIDFILE_9003=/var/run/redis-9003.pid
     CONF_9003="/usr/local/redis-x.x.x/9003/redis.conf"
     
     PIDFILE_9004=/var/run/redis-9004.pid
     CONF_9004="/usr/local/redis-x.x.x/9004/redis.conf"
     
     PIDFILE_9005=/var/run/redis-9005.pid
     CONF_9005="/usr/local/redis-x.x.x/9005/redis.conf"
     
     PIDFILE_9006=/var/run/redis-9006.pid
     CONF_9006="/usr/local/redis-x.x.x/9006/redis.conf"
     
     case "$1" in
         start)
             if [ -f $PIDFILE_9001 ]
             then
                     echo "$PIDFILE_9001 exists, process is already running or crashed"
             else
                     echo "Starting Redis server 9001..."
                     $EXEC $CONF_9001 &
             fi
	     if [ -f $PIDFILE_9002 ]
             then
                     echo "$PIDFILE_9002 exists, process is already running or crashed"
             else
                     echo "Starting Redis server 9002..."
                     $EXEC $CONF_9002 &
             fi
	     if [ -f $PIDFILE_9003 ]
             then
                     echo "$PIDFILE_9003 exists, process is already running or crashed"
             else
                     echo "Starting Redis server 9003..."
                     $EXEC $CONF_9003 &
             fi
	     if [ -f $PIDFILE_9004 ]
             then
                     echo "$PIDFILE_9004 exists, process is already running or crashed"
             else
                     echo "Starting Redis server 9004..."
                     $EXEC $CONF_9004 &
             fi
	     if [ -f $PIDFILE_9001 ]
             then
                     echo "$PIDFILE_9005 exists, process is already running or crashed"
             else
                     echo "Starting Redis server 9005..."
                     $EXEC $CONF_9005 &
             fi
	     if [ -f $PIDFILE_9001 ]
             then
                     echo "$PIDFILE_9006 exists, process is already running or crashed"
             else
                     echo "Starting Redis server 9006..."
                     $EXEC $CONF_9006 &
             fi
             ;;
         stop)
             if [ ! -f $PIDFILE_9001 ]
             then
                     echo "$PIDFILE_9001 does not exist, process is not running"
             else
                     PID=$(cat $PIDFILE_9001)
                     echo "Stopping ..."
                     $CLIEXEC -p $REDISPORT shutdown
                     while [ -x /proc/${PID} ]
                     do
                         echo "Waiting for Redis server 9001 to shutdown ..."
                         sleep 1
                     done
                     echo "Redis server 9001 stopped"
             fi
	     if [ ! -f $PIDFILE_9002 ]
             then
                     echo "$PIDFILE_9002 does not exist, process is not running"
             else
                     PID=$(cat $PIDFILE_9002)
                     echo "Stopping ..."
                     $CLIEXEC -p $REDISPORT shutdown
                     while [ -x /proc/${PID} ]
                     do
                         echo "Waiting for Redis server 9002 to shutdown ..."
                         sleep 1
                     done
                     echo "Redis server 9002 stopped"
             fi
	     if [ ! -f $PIDFILE_9003 ]
             then
                     echo "$PIDFILE_9003 does not exist, process is not running"
             else
                     PID=$(cat $PIDFILE_9003)
                     echo "Stopping ..."
                     $CLIEXEC -p $REDISPORT shutdown
                     while [ -x /proc/${PID} ]
                     do
                         echo "Waiting for Redis server 9003 to shutdown ..."
                         sleep 1
                     done
                     echo "Redis server 9003 stopped"
             fi
	     if [ ! -f $PIDFILE_9004 ]
             then
                     echo "$PIDFILE_9004 does not exist, process is not running"
             else
                     PID=$(cat $PIDFILE_9004)
                     echo "Stopping ..."
                     $CLIEXEC -p $REDISPORT shutdown
                     while [ -x /proc/${PID} ]
                     do
                         echo "Waiting for Redis server 9004 to shutdown ..."
                         sleep 1
                     done
                     echo "Redis server 9004 stopped"
             fi
	     if [ ! -f $PIDFILE_9005 ]
             then
                     echo "$PIDFILE_9005 does not exist, process is not running"
             else
                     PID=$(cat $PIDFILE_9005)
                     echo "Stopping ..."
                     $CLIEXEC -p $REDISPORT shutdown
                     while [ -x /proc/${PID} ]
                     do
                         echo "Waiting for Redis server 9005 to shutdown ..."
                         sleep 1
                     done
                     echo "Redis server 9005 stopped"
             fi
	     if [ ! -f $PIDFILE_9006 ]
             then
                     echo "$PIDFILE_9006 does not exist, process is not running"
             else
                     PID=$(cat $PIDFILE_9006)
                     echo "Stopping ..."
                     $CLIEXEC -p $REDISPORT shutdown
                     while [ -x /proc/${PID} ]
                     do
                         echo "Waiting for Redis server 9006 to shutdown ..."
                         sleep 1
                     done
                     echo "Redis server 9006 stopped"
             fi
             ;;
         restart)
             "$0" stop
             sleep 3
             "$0" start
             ;;
         *)
             echo "Please use start or stop or restart as first argument"
             ;;
     esac
     ```
     > 备注：配置中的 `PIDFILE_xxxx` 为集群对应文件夹 redis.conf 配置文件中的 `pidfile` 配置项的值
     
     设置开机启动：<br/>
     ```
     [root@localhost init.d]# chkconfig redisd on
     ```

     
     

     
     
     

     
    

