####***Tomcat 9 服务部署***####


1. #####安装JDK（以JDK10为例）#####

     1.1. 下载 jdk10 和 jre10
     
     1.2. 移动到 /usr/local 目录
     
     1.3. 提取文件：
      
     ```
     [root@localhost tmp]# tar -zxvf jdk-10.0.2_linux-x64_bin.gz.gz
     [root@localhost tmp]# tar -zxvf jre-10.0.2_linux-x64_bin.gz.gz
     ```
     
     > 注：在jdk10版本中 jdk 和 jre 是分开的，需要分别下载
     

2. #####安装Tomcat 9#####
     2.1. 下载 tomcat
     2.2. 移动到 /usr/local 目录
     2.3. 提取 tomcat：
     ```
     [root@localhost tmp]# tar -zxvf apache-tomcat-9.0.12.tar.gz

     ```

3. #####删除Tomcat自带的管理后台#####
     3.1. 删除 tomcat 目录中 webapps 目录下的所有文件夹
     3.2. 确认 tomcat 目录中 conf 目录下的 tomcat-users.xml 配置文件内 tomcat-users 节点里面的内容是否都已注释
     
4. #####修改Tomcat关闭脚本配置#####
     4.1. 修改 tomcat 目录中 bin 目录下的 shutdown.sh 配置文件，使其支持强制关闭，把
     ```
     exec "$PRGDIR"/"$EXECUTABLE" stop "$@"
     ```
     修改为：
     ```
     exec "$PRGDIR"/"$EXECUTABLE" stop -force "$@"
     ```

5. #####Tomcat单机多实例部署#####
     5.1. 在 opt 目录下创建 tomcat_instance 目录
     5.2. 在 tomcat_instance 目录中创建项目文件夹 example1
     5.3. 复制 tomcat 安装文件夹下的 conf、webapps、temp、logs、work 到创建的项目文件夹下面
     ```
     [root@localhost tmp]# cp -r conf/ webapps/ temp/ logs/ work/ /opt/tomcat_instance/example1

     ```
     5.4 在项目文件夹下面创建 run.sh stop.sh 执行文件
     5.5 打开 run.sh 文件，添加以下内容：
     ```
     export JAVA_HOME=/usr/local/jdk-10.0.2
     export JRE_HOME=/usr/local/jre-10.0.2
     export CATALINA_HOME=/usr/local/tomcat-9.0.12
     export CATALINA_BASE=/opt/tomcat_instance/example1
     export CATALINA_PID=//opt/tomcat_instance/example1/tomcat.pid
     export JVM_OPTIONS="-server -Xms1024m -Xmx16384m -XX:PermSize=128m -XX:MaxPermSize=512m -XX:+UseBiasedLocking"

     sh $CATALINA_HOME/bin/startup.sh
     ```
     5.6 打开 stop.sh 文件，添加以下内容：
     ```
     export JAVA_HOME=/usr/local/jdk-10.0.2
     export JRE_HOME=/usr/local/jre-10.0.2
     export CATALINA_HOME=/usr/local/tomcat-9.0.12
     export CATALINA_BASE=/opt/tomcat_instance/example1
     export CATALINA_PID=//opt/tomcat_instance/example1/tomcat.pid
     export JVM_OPTIONS="-server -Xms1024m -Xmx16384m -XX:PermSize=128m -XX:MaxPermSize=512m -XX:+UseBiasedLocking"

     sh $CATALINA_HOME/bin/shutdown.sh
     ```
     5.7. 设置执行权限：
     ```
     [root@localhost tmp]# chmod +x run.sh
     [root@localhost tmp]# chmod +x stop.sh
     ```
     
6. #####Tomcat优化相关配置#####
     6.1. 打开创建的项目目录并在webapps目录下新建site目录
     6.2. 打开创建的项目目录并转到复制的 conf 文件夹
     6.3. 打开 conf 文件夹下面的 server.xml 配置文件，修改：
     ```
     <Connector port="8080" protocol="HTTP/1.1"
          connectionTimeout="20000"
          redirectPort="8443" />
     ```
     为以下内容：
     ```
     <Connector
          debug="0"
          port="8080"
          redirectPort="8443"
          protocol="org.apache.coyote.http11.Http11NioProtocol"
          maxHttpHeaderSize="8192"
          maxThreads="2000"
          minSpareThreads="100"
          maxSpareThreads="1000"
          enableLookups="false"
          acceptCount="65535"
          disableUploadTimeout="true"
          connectionTimeout="15000"
          compression="on"
          compressionMinSize="5120"
          noCompressionUserAgents="gozilla, traviata"
          compressableMimeType="text/html,text/xml,text/javascript,text/css,text/plain,application/javascript,application/json" />
     ```
     在 Host 节点下添加以下配置：
     ```
     <Context path="" docBase="site" debug="0" reloadable="true" />
     ```
     