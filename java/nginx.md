# Nginx的安装

## 版本区别

常用版本分为四大阵营

- Nginx开源版

  http://nginx.org/

- Nginx plus商业版

  https://www.nginx.com

- openresty

  http://openresty.org/cn/

- Tengine

  http://tengine.taobao.org/

### 编译安装

```bash
./configure --prefix=/usr/local/nginx
```

如果出现警告或报错

提示

```bash
checking for OS
 + Linux 3.10.0-1127.el7.x86_64 x86_64
checking for C compiler ... not found

./configure: error: C compiler cc is not found

```

安装gcc

```bash
yum install -y gcc
```

提示：

```bash

./configure: error: the HTTP rewrite module requires the PCRE library.
You can either disable the module by using --without-http_rewrite_module
option, or install the PCRE library into the system, or build the PCRE library
statically from the source with nginx by using --with-pcre=<path> option.

```

安装perl库

```bash
yum install -y pcre pcre-devel
```

提示：

```bash

./configure: error: the HTTP gzip module requires the zlib library.
You can either disable the module by using --without-http_gzip_module
option, or install the zlib library into the system, or build the zlib library
statically from the source with nginx by using --with-zlib=<path> option.

```

安装zlib库

```bash
yum install -y zlib zlib-devel
```

接下来执行

```bash
make
make install
```

### 启动Nginx

进入安装好的目录

```bash
cd /usr/local/nginx/sbin
```

```bash
./nginx	启动
./nginx -s stop	快速停止
./nginx -s quit	优雅关闭，在退出前完成已经接受的连接请求
./nginx -s reload 重新加载配置
```

### 关于防火墙

关闭防火墙

```bash
systemctl stop firewalld.service
```

禁止防火墙开机启动

```bash
systemctl disable firewalld.service
```

放行端口

```bash
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

重启防火墙

```bash
firewall -cmd --reload
```

### 安装成系统服务

创建服务脚本

```bash
vi /usr/lib/systemd/system/nginx.service
```

服务脚本内容

```bash
[Unit]
Description=nginx - web server
After=network.target remote-fs.target nss-lookup.target
[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s stop
ExecQuit=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true
[Install]
WantedBy=multi-user.target
```

重新加载系统服务

```bash
systemctl daemon-reload
```

这里先停掉刚才启动的nginx

```bash
./nginx -s stop
```

启动服务

```bash
systemctl start nginx.service
```

开机启动

```bash\
systemctl enable nginx.service
```

## 容器（Docker)安装

# Nginx基础使用

## 目录结构

进入Nginx的主目录我们可以看到这些文件夹

```bash
client_body_temp  fastcgi_temp  logs        sbin       uwsgi_temp
conf              html          proxy_temp  scgi_temp
```

其中这几个文件夹在刚安装后是没有的，主要用来存放运行过程中的临时文件

```bash
client_body_temp fastcgi_temp proxy_temp scgi_temp
```

### conf

用来存放配置文件相关

### html

用来存放静态文件的默认目录html、css等

### sbin

nginx的主程序

## 基本运行原理

![Snipaste_2022-04-23_08-21-18](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250951774.jpg)

## Nginx配置与应用场景

### 最小配置

worker_processes  1;

```bash
worker_processes 1
默认为1，表示开启一个业务进程
```

worker_connections

```bash
worker_connections 1024
单个业务进程可接受连接数
```

include       mime.types;

```bash
include mime.types;
引入http mime类型
```

default_type  application/octet-stream;

```bash
default_type application/octet-stream;
如果mime类型没匹配上，默认使用二进制流的方式传输。
```

sendfile        on;

```bash
sendﬁle on
使用linux的sendﬁle(socker,file,len)高效网络传输，也就是数据0拷贝。
```

未开启senﬁle

![image-20220423082757121](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250951397.png)

开启后

![image-20220423082813602](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250951149.png)

keepalive_timeout 65;

```bash
keepalive_timeout 65;
```

server
一个server代表一个主机

![image-20220423082856554](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250951246.png)

虚拟主机配置

```bash
server {
    listen 80; #监听端口号
    server_name localhost; #域名、主机名
    location / { #匹配路径
        root html; #文件根目录
        index index.html index.htm; #默认页名称
    }
    error_page 500 502 503 504 /50x.html; #报错编码对应页面
    location = /50x.html {
    		root html;
		}
}
```

### 虚拟主机

原本一台服务器只能对应一个站点，通过虚拟主机技术可以虚拟化成多个站点同时对外提供服务

#### servername匹配规则

我们需要注意的是servername匹配分先后顺序，写在前面的匹配上就不会继续往下匹配了。

#### 完整匹配

我们可以在同一servername中匹配多个域名

```bash
server_name vod.ceshi.com www1.ceshi.com;
```

#### 通配符匹配

```
server_name *.ceshi.com
```

#### 通配符结束匹配

```
server_name vod.*;
```

#### 正则匹配 

```
server_name ~^[0-9]+\.ceshi\.com$;
```

### 反向代理

描述:反向代理是指以代理服务器来接受连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器,而且整个过程对于客户端而言是透明的。 

![img](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952905.png)

隧道式代理：一进一出一个口



### 正向代理

描述:正向代理意思是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后由代理向原始服务器转交请求并将获得的内容返回给客户端。



简单的说类似于采用VPN来访问google: 

![img](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952137.png)



区别正向代理、反向

都是站在客户端的角度，看代理服务器是帮客户端代理，还是帮服务端代理





### lvs（DR模型）

lvs嵌套在CentOS中

![img](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952077.png)







## 反向代理在系统架构中的应用场景



![img](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952977.png)

![img](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952416.png)





## 负载均衡

描述：负载均衡也是Nginx常用的一个功能。简单而言就是当有2台或以上服务器时，根据规则随机的将请求分发到指定的服务器上处理，负载均衡配置一般都需要同时配置反向代理，通过反向代理跳转到负载均衡。 

而Nginx目前支持自带3种负载均衡策略还有2种常用的第三方策略。

![img](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952445.png)

## 反向代理

### 使用proyx_pass进行代理配置

浏览器访问localhost就会跳转到 http://www.baidu.com,同时域名没有变化（不支持https）

可以有多个server。然后根据策略、调度。

```bash
location / {
	proxy_pass http://www.baidu.com/;
}
```

## 基于反向代理的负载均衡

```bash
    upstream chenfl {
    	server 192.168.56.21:80;
    	server 192.168.56.22:80;
    }
    
    server {
        listen       80;
        server_name  localhost;
        location / {
            proxy_pass http://chenfl;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```

### 负载均衡策略

#### 轮询

默认情况下使用轮询方式，逐一转发，这种方式适用于无状态请求。

#### weight(权重)

指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。示例：

```bash
    
    upstream chenfl {
    	server 192.168.56.21:80 weight=8 down;
   	  server 192.168.56.22:80 weight=2 max_fails=1 fail_timeout=10s max_conns=1024;
      server 192.168.56.23:80 weight=1 backup;
    }
    
    server {
        listen       80;
        server_name  localhost;
        location / {
            proxy_pass http://chenfl;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

```

- down：表示当前的server暂时不参与负载


- weight：默认为1，weight越大，负载的权重就越大。


- backup：其它所有的非backup机器down或者忙的时候，请求backup机器。

- max_fails : 请求失败次数限制
- fail_timeout : 经过max_fails后服务暂停时间
- max_conns : 限制最大的连接数 

#### ip_hash

根据客户端的ip地址转发同一台服务器，可以保持回话。

ip_hash 会话粘连, 上面的2种方式都有一个问题，那就是下一个请求来的时候请求可能分发到另外一个服务器，当我们的程序不是无状态的时候（采用了session保存数据），这时候就有一个很大的很问题了，比如把登录信息保存到了session中，那么跳转到另外一台服务器的时候就需要重新登录了，所以很多时候我们需要一个客户只访问一个服务器，那么就需要用iphash了，iphash的每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。 

示例：

```bash
# 会话粘粘可以理解为用户持续访问一个后端机器
upstream chenfl {
  ip_hash;
  server 192.168.56.21:80;
  server 192.168.56.22:80;
  server 192.168.56.23:80;
} 
```

#### least_conn

最少连接访问

示例：

```bash
upstream  chenfl {
  least_conn;
  server 192.168.56.21:80;
  server 192.168.56.22:80;
  server 192.168.56.23:80;
} 
```

#### url_hash

根据用户访问的url定向转发请求（适用于固定的资源在某台机器上，此时需要把请求落到该机器）

url_hash（第三方）:按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。

在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法 

```bash
upstream chenfl {
  hash $request_uri;
  hash_method crc32;
  server 192.168.56.21:80;
  server 192.168.56.22:80;
  server 192.168.56.23:80;
}
```

#### fair

fair（第三方）按后端服务器 的响应时间来分配请求，响应时间短的优先分配。 

示例：

```bash
upstream chenfl {
  fair;
  server 192.168.56.21:80;
  server 192.168.56.22:80;
  server 192.168.56.23:80;
} 
```

以上6种负载均衡各自适用不同情况下使用，所以可以根据实际情况选择使用哪种策略模式,不过fair和url_hash需要安装第三方模块才能使用

### 动静分离

![img](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952340.png)



动静分离是让动态网站里的动态网页根据一定规则把不变的资源和经常变的资源区分开来，动静资源做好了拆分以后，我们就可以根据静态资源的特点将其做缓存操作，这就是网站静态化处理的核心思路; 

#### 配置反向代理

```bash
location / {
	proxy_pass http://127.0.0.1:8080;
	root html;
	index	index.html	index.htm;
}
```

#### 增加每一个location

```bash
location /css {
	root /usr/local/nginx/static;
	index index.htmlindex.htm;
}
location/images{
	root /usr/local/nginx/static;
	index index.htmlindex.htm;
}
location/js{
	root /usr/local/nginx/static;
	index index.htmlindex.htm;
}
```

#### 使用一个location

使用正则
location前缀

`/`通用匹配，任何请求都会匹配到。

`=`精准匹配，不是以指定模式开头

`~`正则匹配，区分大小写

`~*`正则匹配，不区分大小写

`^~`非正则匹配，匹配以指定模式开头的location

**location匹配顺序**

- 多个正则location直接按书写顺序匹配，成功后就不会继续往后面匹配
- 普通（非正则）location会一直往下，直到找到匹配度最高的（最大前缀匹配）
- 当普通location与正则location同时存在，如果正则匹配成功,则不会再执行普通匹配
- 所有类型location存在时，`=`匹配>`^~`匹配>正则匹配>普通（最大前缀匹配）

```bash
location ~*/(css|img|js) {
  root /usr/local/nginx/static;
  index index.html index.htm;
}
```

#### alias与root

```bash
location /css {
	alias /usr/local/nginx/static/css;
	index index.htmlindex.htm;
}
```

root用来设置根目录，而alias在接受请求的时候在路径上不会加上location。



1）alias指定的目录是准确的，即location匹配访问的path目录下的文件直接是在alias目录下查找的；

2）root指定的目录是location匹配访问的path目录的上一级目录,这个path目录一定要是真实存在root指定目录下的；

3）使用alias标签的目录块中不能使用rewrite的break（具体原因不明）；另外，alias指定的目录后面必须要加上"/"符号！！

4）alias虚拟目录配置中，location匹配的path目录如果后面不带"/"，那么访问的url地址中这个path目录后面加不加"/"不影响访问，访问时它会自动加上"/"；但是如果location匹配的path目录后面加上"/"，那么访问的url地址中这个path目录必须要加上"/"，访问时它不会自动加上"/"。如果不加上"/"，访问就会失败！5）root目录配置中，location匹配的path目录后面带不带"/"，都不会影响访问。

### UrlRewrite

rewrite语法格式及参数语法:

rewrite是实现URL重写的关键指令，根据regex(正则表达式)部分内容，重定向到replacement，结尾是flag标记。

rewrite 	<regex> 	<replacement> 	[flag];
关键字 		正则 				替代内容 			flag标记

正则：perl兼容正则表达式语句进行规则匹配

替代内容：将正则匹配的内容替换成replacement

flag标记：rewrite支持的flag标记

rewrite参数的标签段位置：server,location,if

flag标记说明：

last	#本条规则匹配完成后，继续向下匹配新的locationURI规则

break	#本条规则匹配完成即终止，不再匹配后面的任何规则

redirect	#返回302临时重定向，浏览器地址会显示跳转后的URL地址

permanent	#返回301永久重定向，浏览器地址栏会显示跳转后的URL地址

实例

```bash
 rewrite ^/([0-9]+).html$ /index.jsp?pageNum=$1 break; 
```

同时使用负载均衡
应用服务器防火墙配置

打开防火墙

```
systemctl start firewalld
```

重启防火墙

```
systemctl restart firewalld
```

重载规则

```
firewall-cmd --reload
```

查看已配置规则

```bash
firewall-cmd --list-all
```

指定端口和ip访问

```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.56.21" port protocol="tcp" port="8080" accept"
```

移除规则

```防盗链
upstream httpds{
    server 192.168.44.102 weight=8down;
    server 192.168.44.103:8080 weight=2;
    server 192.168.44.104:8080 weight=1backup;
}
location /{
    rewrite ^/([0-9]+).html$ /index.jsp?pageNum=$1 redirect;
    proxy_pass http://httpds;
}
```

## 防盗链

**http协议中的 referrer**

**nginx防盗链配置**

**使用浏览器或curl检测**

**返回错误码**

**返回错误页面**

**整合rewrite返回报错图片** 

### 防盗链配置

```bash
valid_referers none | blocked | server_names | strings ....;
```

- none，检测Referer头域不存在的情况。
- blocked，检测Referer头域的值被防火墙或者代理服务器删除或伪装的情况。这种情况该头域的值不以“http://”或“https://”开头。
- server_names，设置一个或多个URL，检测Referer头域的值是否是这些URL中的某一个。
  在需要防盗链的location中配置

```bash
valid_referers 192.168.56.20;
if($invalid_referer){
		return 403;
}
```

使用curl测试(方便测试，因为浏览器可能经常存在缓存)

```bash
curl -I http://192.168.56.20/img/logo.png
```

带引用

```bash
curl -e "http://baidu.com" -I http://192.168.56.20/img/logo.png
```

## 高可用配置



![img](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952126.png)



### 安装Keepalived

#### 编译安装

下载地址

https://www.keepalived.org/download.html#

使用`./configure`编译安装

如遇报错提示：

```bash
configure: error:
!!! OpenSSL is not properly installed on your system. !!!
!!! Can not include OpenSSL headers files. !!!
```

安装依赖

```bsh
yum install openssl-devel
```

#### yum安装

```bash
yum install keepalived
```

### 配置

使用yum安装后配置文件在

```bash
/etc/keepalived/keepalived.conf
```

#### 最小配置

第一台机器

```bash
! Configuration File forkeepalived
global_defs{
	router_id lb111
}
vrrp_instanceatguigu{
        state MASTER
        interface ens33	#网卡名字（ens33）
        virtual_router_id 51
        priority 100	#优先级
        advert_int 1
    authentication{
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress{
        192.168.44.200
    }
}
```

第二台机器

```bash
! Configuration File forkeepalived
global_defs{
	router_id lb110
}
vrrp_instance atguigu{
	state BACKUP
	interface ens33
	virtual_router_id 51
	priority 50
	advert_int 1
	authentication{
	auth_type PASS
	auth_pass 1111
}
virtual_ipaddress{
	192.168.44.200
}
}
```

启动服务

```bash
systemctl start keepalived
```

## Https证书配置

### 不安全的http协议

![image-20220423091121437](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952356.png)

### openssl

openssl包含：SSL协议库、应用程序以及密码算法库

### 自签名

#### OpenSSL

系统内置

#### 图形化工具XCA

下载地址

https://www.hohnstaedt.de/xca/index.php/download

### CA 签名


![image.png](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952832.png)

 ca机构参与保证互联网安全 

[什么是证书颁发机构（CA）](https://blog.csdn.net/weixin_33964094/article/details/85100922?ops_request_misc=%7B%22request%5Fid%22%3A%22165052460216782246417186%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165052460216782246417186&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-85100922.142^v9^control,157^v4^control&utm_term=ca机构&spm=1018.2226.3001.4187)

[CA证书简单介绍](https://blog.csdn.net/Munch_D_Rudy/article/details/114337721?ops_request_misc=%7B%22request%5Fid%22%3A%22165052451316780261916415%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165052451316780261916415&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-114337721.142^v9^control,157^v4^control&utm_term=ca&spm=1018.2226.3001.4187)

![image.png](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952111.png)

 证书安装 

先使用域名申请证书。

将证书放到/usr/local/nginx/conf目录中。

ssl_certificate			xx.pem;

ssl_certificate_key		xx.key;

●安装到nginx

![image.png](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/nginx/img/202204250952331.png)

现在访问 server_name 对应的域名，即可安全