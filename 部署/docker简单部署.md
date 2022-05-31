# 一、使用docker部署一个前端项目

## 1.创建一个工作的目录

```bash
mkdir /mywork
```

## 2.项目进行打包

```bash
npm run build
```

会生产一个dist文件夹，如下，将打包好的dist文件上传到/mywork目录下

![image-20220530133834932](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/docker%E7%AE%80%E5%8D%95%E9%83%A8%E7%BD%B2/img/202205301428239.png)

## 3.准备nginx配置文件

为了方便管理，创建的配置文件也放置在/mywork目录下，`nginx.conf.template`文件内容如下

```bash
user nginx;
 
#user  nobody;
worker_processes  1;
 
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
 
#pid        logs/nginx.pid;
 
 
events {
    worker_connections  1024;
}
 
 
http {
    include       mime.types;
    default_type  application/octet-stream;
 
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
 
    #access_log  logs/access.log  main;
 
    sendfile        on;
    #tcp_nopush     on;
 
    #keepalive_timeout  0;
    keepalive_timeout  65;
 
    #gzip  on;
 
    server {
        listen       80;
        server_name  localhost;
 
        #charset koi8-r;
 
        #access_log  logs/host.access.log  main;
 
        location / {
            root   /dist;
			#try_files $uri /index.html; #解决路由重定向跳转 404 页面配置
            index  index.html index.htm;
        }
 
        #error_page  404              /404.html;
 
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
 
 
    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;
 
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
 
 
    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;
 
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;
 
    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;
 
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;
 
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
 
}
```

## 4.准备Dockerfile文件

将该文件也放在/mywork目录下

`Dockerfile`内容如下

```bash
FROM nginx:latest
 
RUN mkdir /dist
COPY ./dist /dist
COPY ./nginx.conf.template /
 
CMD envsubst < /nginx.conf.template > /etc/nginx/nginx.conf \
	&& cat /etc/nginx/nginx.conf \
	&& nginx -g 'daemon off;'
```

该文件里主要是安装了nginx，复制dist、nginx.conf.template、启动nginx的步骤。

到此有这些文件如下

![image-20220530140724521](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/docker%E7%AE%80%E5%8D%95%E9%83%A8%E7%BD%B2/img/202205301428055.png)

## 5.构建镜像

先进入到该工作目录下

```bash
cd /mywork
```

正式构建镜像

```bash
docker build -f Dockerfile -t my-webtest:v1.0 .
```

成功如下

![image-20220530140918039](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/jvm/img/202205301432239.png)

## 6.运行该容器

```bash
docker run -d --name nginx01 -p 3000:80  my-webtest:v1.0
# -d 后台运行
# --name nginx01  设置容器名字叫nginx01
# -p 3000:80 公网ip的3000端口对应该容器的80端口
# my-webtest:v1.0 构建的镜像的名字
```

docker logs 容器id 可以查看容器日志

到此部署成功，服务器ip:3000测试访问

# 二、使用docker部署一个后端项目

## 1.创建一个工作目录

## 2、先把java项目使用maven插件打包成jar包。

```bash
mvn package
```

## 3、将.jar包放在工作目录下

## 3、新建Dockerfile文件

将该文件也放在创建的工作目录下

`Dockefile`文件内容：

```bash
# java11运行环境
FROM openjdk:11
# 作者名称
MAINTAINER chenfl
 
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

## 4、构建镜像

切换至工作目录，构建镜像：

```bash
docker build -t demo:0.0.1 .
```

说明：
-t 参数指定构建成后，镜像的仓库、标签等
demo:0.0.1 demo镜像名称、0.0.1 镜像标签

## 5、运行镜像

```bash
docker run -p 8080:8080 -t demo:0.0.1
```