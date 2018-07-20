####***Redis集群搭建***####


1. #####下载安装包#####
     [下载地址](https://redis.io/download)
2. #####提取安装包#####
     `tar -zxvf redis.x.x.x.tar.gz`
3. #####编译并安装#####
     转到提取目录
     ```
     make
     make PERFIX=/usr/local/redis.x.x.x/bin install
     ```
     > 注： `PERFIX` 指定要安装到的目录，不指定则默认安装到 `/usr/local/bin`
4. #####创建集群文件夹#####
     在 `/usr/local/redis.x.x.x` 文件夹下创建 9001~9006 文件夹<br/>
     `mkdir -p 9001/data 9002/data 9003/data 9004/data 9005/data 9006/data`
5. #####复制Redis配置文件#####
     把提取的安装包文件夹中的 `redis.conf` 配置文件复制到刚才创建的集群文件夹中<br/>
     `cp redis.conf /usr/local/redis.x.x.x/9001`
6. #####修改Redis配置文件#####
     
     

     
    

