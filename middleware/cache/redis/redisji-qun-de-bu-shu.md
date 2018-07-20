####***Redis集群搭建***####


1. #####下载安装包#####
     [下载地址](https://redis.io/download)
2. #####提取安装包#####
     `tar -zxvf redis.x.x.x.tar.gz`
3. #####编译并安装#####
     转到提取目录：
     ```
     make
     make PERFIX=/usr/local/redis.x.x.x/bin install
     ```
     > 注： `PERFIX` 指定要安装到的目录，不指定则默认安装到 `/usr/local/bin`
4. #####创建集群文件夹#####
     在 `/usr/local/redis.x.x.x` 文件夹下创建 9001~9006 文件夹：<br/>
     `mkdir -p 9001/data 9002/data 9003/data 9004/data 9005/data 9006/data`
5. #####复制Redis配置文件#####
     把提取的安装包文件夹中的 `redis.conf` 配置文件复制到刚才创建的集群文件夹中：<br/>
     `cp redis.conf /usr/local/redis.x.x.x/9001`
6. #####修改Redis配置文件#####
     转到集群文件夹：<br/>
     `cd /usr/local/redis.x.x.x/9001`<br/>
     编辑配置文件：<br/>
     `vi redis.conf`<br/>
     修改配置文件以下配置项：<br/>
     ```
     #本节点的端口号
     port 9001
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
     cluster-node-timeout 15000
     appendonly yes
     ```
7. #####复制修改后的配置文件到其它集群文件夹#####
     ```
     cp redis.conf /usr/local/redis.x.x.x/9002
     ...
     cp redis.conf /usr/local/redis.x.x.x/9006
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
     redis-server /usr/local/redis.x.x.x/9001/redis.conf
     ...
     redis-server /usr/local/redis.x.x.x/9006/redis.conf
     ```
     检测各个节点是否已启动：<br/>
     `ps -el | grep redis`
     ```
     5 S     0   401     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     5 S     0   462     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     5 S     0   485     1  0  80   0 - 36489 ep_pol ?        00:00:10 redis-server
     5 S     0   523     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     5 S     0   615     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     5 S     0  1233     1  0  80   0 - 36489 ep_pol ?        00:00:09 redis-server
     ```
9. #####安装创建集群所需软件#####


     
     
     

     
    

