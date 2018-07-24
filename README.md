Centos服务器配置
安装jdk：
1使用yum安装jdk，先配置阿里云yum源不然下载可能很慢。 (下载源修改教程 https://blog.csdn.net/ltx06/article/details/78030056) 2安装命令： 查看所有版本：jdk  yum -y list java*  选择1.8 ，安装所有java程序：yum install java-1.8.0-openjdk* 完成之后：java –version 查看版本情况
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

 临时密码登录：mysql -uroot -p

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
修改系统时间时区：timedatectl set-timezone Asia/Shanghai
同步网络时间：ntpdate us.pool.ntp.org



docker使用
服务安装：yum install docker
	修改镜像下载地址，添加"https://registry.docker-cn.com"到registry-mirrors数组中/etc/docker/daemon.json
