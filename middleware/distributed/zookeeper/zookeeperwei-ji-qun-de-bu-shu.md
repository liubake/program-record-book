####***Zookeeper伪集群搭建***####


1. #####下载安装包#####
     [下载地址](https://www.apache.org/dyn/closer.cgi)
2. #####提取安装包#####
     ```
     [root@localhost tmp]# tar -zxvf zookeeper.x.x.x.tar.gz
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

12. #####设置开机启动#####
     创建启动脚本文件：<br/>
     ```
     [root@localhost /]# touch /etc/init.d/redisd
     ```
     编写启动脚本：<br/>
     ```
     
     ```
     > 备注：配置中的 `PIDFILE_xxxx` 为集群对应文件夹 redis.conf 配置文件中的 `pidfile` 配置项的值
     
     设置执行权限：<br/>
     ```
     [root@localhost init.d]# chmod a+x redisd
     ```
     
     设置开机启动：<br/>
     ```
     [root@localhost init.d]# chkconfig redisd on
     ```
     
