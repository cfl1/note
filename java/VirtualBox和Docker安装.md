# 1、安装Linux虚拟机

- 下载&安装VirtualBoxhttps://www.virtualbox.org/，要开启CPU虚拟化

![Snipaste_2022-03-03_12-06-40]( https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/VirtualBox%E5%92%8CDocker%E5%AE%89%E8%A3%85/img/Snipaste_2022-03-03_12-06-40.jpg)

![Snipaste_2022-03-03_12-07-03]( https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/VirtualBox%E5%92%8CDocker%E5%AE%89%E8%A3%85/img/Snipaste_2022-03-03_12-07-03.jpg)

- 下载&安装Vagrant
  - https://app.vagrantup.com/boxes/search Vagrant官方镜像仓库
  - https://www.vagrantup.com/downloads.html Vagrant下载

- 打开window cmd窗口，运行

  ```bash
  Vagrant init centos/7
  ```

  即可初始化一个centos7系统，在哪个目录下执行就安装在哪

- 运行

  ```bash
  vagrant up
  ```

  即可启动虚拟机。系统root用户的密码是vagrant

- vagrant其他常用命令

  - ```bash
    vagrant ssh
    ```

    自动使用vagrant用户连接虚拟机。

    - ```bash
      vagrant upload source [destination][name|id]
      ```

      上传文件

  - https://www.vagrantup.com/docs/cli/init.html Vagrant 命令行

- 默认虚拟机的ip地址不是固定ip，开发不方便

  - 修改Vagrantfile

    - config.vm.network "private_network", ip: "192.168.56.10"  

    - 这里的ip需要在物理机下使用ipconfig命令找到![Snipaste_2022-03-03_12-07-13]( https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/VirtualBox%E5%92%8CDocker%E5%AE%89%E8%A3%85/img/Snipaste_2022-03-03_12-07-13.jpg)改为这个指定的子网地址

    - 重新使用

      ```bash
      vagrant up
      ```

      启动机器即可。然后再

      ```bash
      vagrant ssh
      ```

      连接机器

- 默认只允许ssh登录方式，为了后来操作方便，文件上传等，我们可以配置允许账号密码登录

  - Vagrant ssh进去系统之后

    ```bash
    vi /etc/ssh/sshd_config
    ```

    修改PasswordAuthentication yes

    重启服务
    
    ```bash
    service sshd restart
    ```

- 以后可以使用提供的ssh连接工具直接连接

- 注意：VirtualBox会与包括但不限于如下软件冲突，需要卸载这些软件，然后重启电脑；冲突的软件：红蜘蛛，360，净网大师（有可能）等

- 修改linux的yum源

  - 备份原yum

    ```bash
    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
    ```

  - 使用新yum源

    ```bash
    curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
    ```

  - 生成缓存

    ```bash
    yum makecache
    ```

# 2、安装docker

Docker安装文档：https://docs.docker.com/install/linux/docker-ce/centos/

- 卸载系统之前的docker

  ```bash
  sudo yum remove docker \
  docker-client \
  docker-client-latest \
  docker-common \
  docker-latest \
  docker-latest-logrotate \
  docker-logrotate \
  docker-engine
  ```

- 2、安装Docker-CE

  - 安装必须的依赖

    ```bash
    sudo yum install -y yum-utils \ device-mapper-persistent-data \ lvm2
    ```
    
  - 设置docker repo的yum位置

    ```bash
  sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    ```
  
  - 安装 docker， 以及 docker-cli

    ```bash
  sudo yum install docker-ce docker-ce-cli containerd.io
    ```
  
  - 启动 docker 

    ```bash
  sudo systemctl start docker
    ```
  
  - 设置 docker 开机自启  

    ```bash
  sudo systemctl enable docker
    ```
  
  - 测试 docker 常用命令， 注意切换到 root 用户下  

    https://docs.docker.com/engine/reference/commandline/docker/  

  - 配置 docker 镜像加速

    阿里云，容器镜像服务针对 Docker 客户端版本大于 1.10.0 的用户,您可以通过修改 daemon 配置文件

    ```bash
  /etc/docker/daemon.json 
    ```
  
    来使用加速器  

    ```bash
  sudo mkdir -p /etc/docker
    sudo tee /etc/docker/daemon.json <<-'EOF'
    {
      "registry-mirrors": ["https://5955cm2y.mirror.aliyuncs.com"]
    }
    EOF
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```

# 3、运行常使用容器

其他见各笔记，或官方文档

## docker 安装 mysql  

- 下载镜像文件  

  - ```bash
    docker pull mysql:8.0
    ```

- 创建实例并启动

  - ```bash
    docker run -p 3306:3306 --name mysql \
    -v /mydata/mysql/mysql-files:/var/lib/mysql-files \
    -v /mydata/mysql/log:/var/log/mysql \
    -v /mydata/mysql/data:/var/lib/mysql \
    -v /mydata/mysql/conf:/etc/mysql \
    -e MYSQL_ROOT_PASSWORD=123456 \
    -d mysql:8.0
    ```
  ```
    
    **参数说明**
    -p 3306:3306： 将容器的 3306 端口映射到主机的 3306 端口
    -v /mydata/mysql/conf:/etc/mysql： 将配置文件夹挂载到主机
    -v /mydata/mysql/log:/var/log/mysql： 将日志文件夹挂载到主机
    -v /mydata/mysql/data:/var/lib/mysql/： 将配置文件夹挂载到主机
  -e MYSQL_ROOT_PASSWORD=root： 初始化 root 用户的密码  
    
  - MySQL 配置  
  
    ```bash
    vi /mydata/mysql/conf/my.cnf
  ```

    ```text
    [client]
    default-character-set=utf8
    [mysql]
    default-character-set=utf8
    [mysqld]
    init_connect='SET collation_connection = utf8_unicode_ci'
    init_connect='SET NAMES utf8'
    character-set-server=utf8
    collation-server=utf8_unicode_ci
    skip-character-set-client-handshake
    skip-name-resolve
    ```

    **注意：** 解决 MySQL 连接慢的问题
    在配置文件中加入如下， 并重启 mysql
    [mysqld]
    skip-name-resolve
    解释：
    skip-name-resolve： 跳过域名解析  

- 通过容器的 mysql 命令行工具连接  

  ```bash
  docker exec -it mysql mysql -uroot -proot
  ```

- 设置 root 远程访问  

  ```bash
  grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;
  flush privileges;
  ```

- 进入容器文件系统 

  ```bash
  docker exec -it mysql /bin/bash
  ```

- 容器自启动

  ```bash
  docker update --restart=always 容器ID或名称
  ```

  

## docker 安装 redis

- 下载镜像文件

  ```bash
  docker pull redis
  ```

- 创建实例并启动

  ```bash
  mkdir -p /mydata/redis/conf
  touch /mydata/redis/conf/redis.conf
  docker run -p 6379:6379 --name redis -v /mydata/redis/data:/data \
  -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
  -d redis redis-server /etc/redis/redis.conf
  ```

  配置持久化：Redis默认是不持久化的。在配置文件redis.conf中添加:  

  ```bash
  appendonly yes
  ```

  redis 自描述文件：  

  https://raw.githubusercontent.com/antirez/redis/4.0/redis.conf  

- 使用 redis 镜像执行 redis-cli 命令连接 

  ```bash
  docker exec -it redis redis-cli
  ```

## docker 安装 nacos

```bash
docker run --env MODE=standalone --name nacos \
-v /mydata/nacos/conf:/home/nacos/conf -d -p 8848:8848 nacos/nacos-server:1.1.4
```

## docker 安装 sentinel

可以制作一个镜像并启动它，暴露访问

```bash
docker run --name sentinel -d -p 8858:8858 -d bladex/sentinel-dashboard:1.6.3
```

## docker 安装 zipkin

```bash
docker run -d -p 9411:9411 openzipkin/zipkin
或者
docker run --env STORAGE_TYPE=elasticsearch --env ES_HOSTS=180.76.181.210:9200 openzipkin/zipkin
```

# 4、docker打包镜像

## 构建镜像

1、先把java项目使用maven插件打包成jar包。

2、将.jar包放在有docker环境的服务器中

3、先将.jar文件放入指定目录，并新建Dockerfile文件

Dockerfile文件内容：

```bash
# java8运行环境
FROM openjdk:11
# 作者名称
MAINTAINER Dome
 
# 切换工作目录
WORKDIR /root/java
 
# 添加demo-start-1.0.0.jar文件到docker环境内
ADD demo-start-1.0.0.jar /root/java/demo-start-1.0.0.jar

# 暴露端口8080
EXPOSE 8080

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 运行命令
ENTRYPOINT ["java", "-server", "-Xms256m", "-Xmx256m", "-jar", "/root/java/demo-start-1.0.0.jar"]
```

4、在当前目录下运行指令，构建镜像

```bash
docker build -t demo:0.0.1 .
```

说明：
-t 参数指定构建成后，镜像的仓库、标签等
demo:0.0.1   demo镜像名称、0.0.1 镜像标签

5、直接运行镜像试试能不能成功

```bash
docker run -p 8080:8080 -t demo:0.0.1
```

## Docker镜像上传到仓库

1、先创建Docker hub账号，记住username password后面验证时，需要使用到。

2、先登录Docker hub

```bash
docker login -u username -p password
```

3、将构建好的镜像打上标签

```bash
docker tag demo:0.0.1 username/demo:0.0.1
```

说明:
docker tag 	SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
             			本地镜像名称:版本号  DockerHub账号名/镜像名称:版本号



 注：

如果需要上传到其他镜像创建上面命令的改动如下（以阿里云为例子）：

先登录：

```bash
docker login -u username [registry.cn-shenzhen.aliyuncs.com](<http://registry.cn-shenzhen.aliyuncs.com/>)
```

回车后输入密码，则登录成功

打标记：

```bash
docker tag demo:0.0.1 [registry.cn-shenzhen.aliyuncs.com](<http://registry.cn-shenzhen.aliyuncs.com/>)/username/demo:0.0.1
```

上传镜像：

```bash
docker push [registry.cn-shenzhen.aliyuncs.com](<http://registry.cn-shenzhen.aliyuncs.com/>)/username/demo:0.0.1
```