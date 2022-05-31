# 一、使用yum安装jdk

在linux上使用yum安装还是比较方便的

- 1.执行命令`yum -y list java*`查看可安装java版本。执行成功后可以看见如下的结果

  ![image-20220528204024091](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/linux%E5%AE%89%E8%A3%85jdk/img/202205282100937.png)

  有问题的话可以尝试重新建立yum

  ```bash
  wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
  
  wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
  
  yum clean all  #清除所有
  
  yum makecache #重新建立源数据
  ```

  如果有报yum命令出现Loaded plugins: fastestmirror,则：

  修改插件的配置文件

  ```bash
  vi /etc/yum/pluginconf.d/fastestmirror.conf`
  ```


  将`enabled=1`改为`enabled=0`

  修改yum的配置文件

  ```bash
  vi /etc/yum.conf
  ```

  将`plugins=1`改为`plugins=0`

  清除缓存

  ```bash
  yum clean all
  yum clean dbcache
  yum makecache
  ```

- 2.选择一个java版本进行安装，这里我们希望安装java11，因为我们的机器是64位的，所以选择安装java-11-openjdk-devel.x86_64。

  这里有个地方要注意，上图中我用红框圈起来的两个java版本，要选择-devel的安装，因为这个安装的是jdk，而那个不带-devel的安装完了其实是jre。

- 3.执行命令` yum install -y java-11-openjdk-devel.x86_64`。执行完后会看见控制台刷出很多输出。

  耐心等待至自动安装完成

  ![image-20220528204241616](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/linux%E5%AE%89%E8%A3%85jdk/img/202205282100596.png)

- 4.输入`java -version`查看已安装的jdk版本，当出现如下输出表示安装成功。

  ![image-20220528204313867](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/linux%E5%AE%89%E8%A3%85jdk/img/202205282100727.png)

- 5.你可能好奇，yum安装的jdk，被安装到哪里去了？你可以在/usr/lib/jvm目录下找到他们。

  ![image-20220528204414557](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/java/linux%E5%AE%89%E8%A3%85jdk/img/202205282100688.png)

至此，yum安装jdk完成。

# 二、安装包的方式

- **1、检查一下系统中的jdk版本**

  ```bash
  java -version
  ```

  **显示：**

  ```bash
  openjdk version "1.8.0_102"
  OpenJDK Runtime Environment (build 1.8.0_102-b14)
  OpenJDK 64-Bit Server VM (build 25.102-b14, mixed mode)
  ```

- **2、检测jdk安装包**

  ```bash
  rpm -qa | grep java
  ```

  **显示：**

  ```bash
  java-1.7.0-openjdk-1.7.0.111-2.6.7.8.el7.x86_64
  python-javapackages-3.4.1-11.el7.noarch
  tzdata-java-2016g-2.el7.noarch
  javapackages-tools-3.4.1-11.el7.noarch
  java-1.8.0-openjdk-1.8.0.102-4.b14.el7.x86_64
  java-1.8.0-openjdk-headless-1.8.0.102-4.b14.el7.x86_64
  java-1.7.0-openjdk-headless-1.7.0.111-2.6.7.8.el7.x86_64
  ```

- **3、卸载openjdk**

  ```bash
  rpm -e --nodeps tzdata-java-2016g-2.el7.noarch
  rpm -e --nodeps java-1.7.0-openjdk-1.7.0.111-2.6.7.8.el7.x86_64
  rpm -e --nodeps java-1.7.0-openjdk-headless-1.7.0.111-2.6.7.8.el7.x86_64
  rpm -e --nodeps java-1.8.0-openjdk-1.8.0.102-4.b14.el7.x86_64
  rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.102-4.b14.el7.x86_64
  ```

  或者使用

  ```bash
  yum remove *openjdk*
  ```

  之后再次输入`rpm -qa | grep java` 查看卸载情况：

  ```bash
  python-javapackages-3.4.1-11.el7.noarch
  javapackages-tools-3.4.1-11.el7.noarch
  ```

- **4、安装新的jdk**

  首先到jdk官网上下载你想要的jdk版本，下载完成之后将需要安装的jdk安装包放到Linux系统指定的文件夹下

  慢的话可以使用镜像地址如华为：

  ```bash
  curl -O https://repo.huaweicloud.com/java/jdk/11+28/jdk-11_linux-x64_bin.tar.gz
  ```

  命令进入该文件夹下：

  解压 jdk-11_linux-x64_bin.tar.gz安装包

  ```bash
  mkdir -p /usr/lib/jvm
  tar -zxvf jdk-11_linux-x64_bin.tar.gz -C /usr/lib/jvm
  ```

- **5、设置环境变量**

  ```bash
  vim /etc/profile
  ```

  在最前面添加：

  ```bash
  export JAVA_HOME=/usr/lib/jvm/jdk-11
  export JRE_HOME=${JAVA_HOME}/jre  
  export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
  export  PATH=${JAVA_HOME}/bin:$PATH
  ```

- **6、执行profile文件**

  ```bash
  source /etc/profile
  ```

  这样可以使配置不用重启即可立即生效。

- **7、检查新安装的jdk**

  ```bash
  java -version
  ```

  显示：

  ```bash
  java version "11"
  Java(TM) SE Runtime Environment (build 1.8.0
  openjdk version "11.0.15" 2022-04-19 LTS
  OpenJDK Runtime Environment 18.9 (build 11.0.15+9-LTS)
  OpenJDK 64-Bit Server VM 18.9 (build 11.0.15+9-LTS, mixed mode, sharing)
  _131-b11)
  Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
  ```

  到此为止，整个安装过程结束。