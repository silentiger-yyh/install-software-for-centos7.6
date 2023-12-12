# 安装Docker
```sh
$ wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
$ yum -y install docker-ce-18.06.1.ce-3.el7
$ systemctl enable docker && systemctl start docker
$ docker --version
Docker version 18.06.1-ce, build e68fc7a
```
# Docker部署持久化MySQL5.7
## 需要挂载的目录结构
```shell
mysql5.7
   conf
     my.cnf
   logs
   data
```
## my.cnf
```shell
[client]
# 设置mysql客户端连接服务端时默认字符集
default-character-set=utf8mb4
[mysql]
#设置mysql客户端默认字符集
default-character-set=utf8mb4
[mysqld]
sql-mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
#服务端使用的字符集默认为utf8mb4
character-set-server=utf8mb4
#允许最大连接数
max_connections=1000
#允许连接失败的次数
max_connect_errors=10
#创建新表时将使用的默认存储引擎
default-storage-engine=INNODB

```
## 拉取mysql5.7镜像
```shell
[root@localhost mysql57]# docker pull mysql:5.7
5.7: Pulling from library/mysql
11a38aebcb7a: Pull complete 
91ab01309bd6: Pull complete 
6c91fabb88c2: Pull complete 
8f46e806ab5c: Pull complete 
29f5af1d1661: Pull complete 
62aca7179a54: Pull complete 
85023e6de3be: Pull complete 
6d5934a87cbb: Pull complete 
c878502d3f70: Pull complete 
4756467c684a: Pull complete 
ee9043dd2677: Pull complete 
Digest: sha256:f566819f2eee3a60cf5ea6c8b7d1bfc9de62e34268bf62dc34870c4fca8a85d1
Status: Downloaded newer image for mysql:5.7

```
## 创建并启动容器
```shell
docker run --restart=always -p 3306:3306 --name mysql5.7 -v /data/mysql57/conf/my.cnf:/etc/mysql/my.cnf -v /data/mysql57/logs:/var/log/mysql  -v /data/mysql57/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7

参数说明：
--restart=always 当docker重启时，容器也重启
--name 容器命名
-p 3306:3306 宿主机和容器端口映射
-v（此命令中有三个-v参数） 挂载数据等内容，避免容器重启造成的数据丢失
-e 设置环境变量（此处设置了mysql的密码）
-d 后台启动（容器不会因为shell的退出而停止运行）
```








