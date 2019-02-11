Centos服务器配置
安装jdk：
1使用yum安装jdk，先配置阿里云yum源不然下载可能很慢。
(下载源修改教程 https://blog.csdn.net/ltx06/article/details/78030056)
2安装命令：
查看所有版本：jdk  yum -y list java* 
选择1.8 ，安装所有java程序：yum install java-1.8.0-openjdk*
完成之后：java –version 查看版本情况 
安装mysql
1默认yum源没有mysql，下载mysql的repo源
下载：wget https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
安装：
rpm –ivh mysql80-community-release-el7-1.noarch.rpm
查看版本：yum repolist all | grep mysql
 
默认最高版本修改版本：
命令：vi /etc/yum.repos.d/mysql-community.repo
 
安装服务：sudo yum install mysql-community-server
启动mysql：sudo service mysqld start
查看状态：sudo service mysqld status
 
设置管理员密码：
 先获取临时密码：sudo grep 'temporary password' /var/log/mysqld.log
 
 临时密码登录：mysql –uroot -p
 
修改root密码：ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';
修改使外网能访问：update mysql.user  set Host='%' where User='root';
修改端口号：vi /etc/my.cnf  增加porn=57773 
重启：service mysqld restart

安装nginx
因为nginx非centos自带软件包
获取源：sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

安装：sudo yum install -y nginx
 
默认配置文件在/etc/nginx/目录下
启动Nginx并设置开机自动运行
sudo systemctl start nginx.service
sudo systemctl enable nginx.service

安装缓存
搜索安装包：yum search memcached
安装：yum -y install memcached
启动memcache的服务器：
memcached -d -m 1000 -u root -p 11211 -P /tmp/memcached.pid
参数说明：
-d选项是启动一个守护进程；
-m是分配给memcache使用的内存数量，单位是mB，我这里是1000mB；
-u是运行memcache的用户，我这里是root；
-l是监听的服务器IP地址；
-p是设置memcache监听的端口，我这里设置了11211，最好是1024以上的端口；
-c选项是最大运行的并发连接数，默认是1024，按照你服务器的负载量来设定；
-P是设置保存memcache的pid文件，我这里是保存在 /tmp/memcached.pid

常用命令：
service memcached start
service memcached stop
service memcached restart

设置开机启动：
systemctl enable memcached.service



服务器设置
关闭防火墙：systemctl stop firewalld
安装iptables：yum -y install iptables-services
端口配置文件：etc/sysconfig/iptables
重启防火墙：systemctl restart iptables
修改系统时间时区：timedatectl set-timezone Asia/Shanghai
同步网络时间：ntpdate us.pool.ntp.org



docker使用
服务安装：yum install docker
	启动服务：systemctl start docker 
	修改镜像下载地址，添加"https://registry.docker-cn.com"到registry-mirrors数组中/etc/docker/daemon.json
	
--privileged=true  给容器加特权
JAVA环境
下载所需要的基础镜像：docker pull java:8 （类似git 命令java:8可替换对应的地址）
	查看本地镜像：docker images
	编写Dockerfile：
-----------
#使用镜像
FROM java:8
#VOLUME /tmp创建/tmp目录并持久化到Docker数据文件夹，
#因为Spring Boot使用的内嵌Tomcat容器默认使用/tmp作为工作目录
VOLUME /tmp
#将指定的jar文件添加到容器中
ADD docker-demo-0.0.1-SNAPSHOT.jar app.jar
RUN bash -c 'touch /app.jar'
#执行jar文件
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
------------
	创建镜像：docker build -t tmy/spring-boot-app .（build后面可以指定路径下的Dockerfile文件，. 是默认路径 ，t后是新镜像名称）
	

nginx环境：
下载nginx镜像：docker search nginx;  docker pull nginx;
创建对应的资源文件夹和.conf文件
demo.conf:
---------------
server{
	listen 80;
	server_name demo.com
	location / {
		root /usr/share/nginx/html;
		index index.heml;
}
}
---------------

配置文件映射：-v $PWD: /usr/share/nginx/html  
-v $PWD: /etc/nginx/conf.d
$PWD表示当前目录
启动命令：docker run –name nginx1 –d –p 80:80 -v $PWD: /etc/nginx/conf.d nginx


MYSQL环境
下载镜像：docker pull mysql:5.7
启动镜像：
docker run --rm --name mysql -it -v /docker/mysql/data/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=titlis123  mysql:5.7 /bin/bash
（--rm是告诉Docker一旦运行的进程退出就删除容器）
拷贝配置文件：cp /etc/mysql/my.cnf /var/lib/mysql（方便之后修改）
退出：exit
启动容器：docker run --name mysqlserver -p 3306:3306 –v  $PWD/data: /var/lib/mysql -v $PWD/my.cnf: /etc/mysql/my.cnf -d mysql:5.7



docker run --name mysqlserver -v /docker/mysql/data/:/var/lib/mysql -v /docker/mysql/my.cnf:/etc/mysql/my.cnf --privileged=true  -e MYSQL_ROOT_PASSWORD=titlis123 -d  mysql:5.7



Redis安装
下载：docker pull redis
运行：docker run -p 6379:6379 --privileged=true -d  -v /redis/data:/data redis --appendonly yes (启动持久化数据)
