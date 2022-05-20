# gitee+typora+picgo

## gitee

### 1. 登录码云账号建立仓库

gitee官网：https://gitee.com

没有自行注册一个，然后新建仓库

![image-20220326174919779](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202203261749482.png)

### 2.设置私人令牌

![image-20220326175046100](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202203261751646.png)

复制生产后的token

## picgo

PicGo下载地址：https://github.com/Molunerfinn/picgo/releases

### 1.安装后打开，安装gitee插件

![image-20220326175324081](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202203261753330.png)

### 2.设置gitee图床

![image-20220326175717171](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202203261800081.png)

repo:码云上面我们的仓库路径一般是这样的格式:用户名/仓库名
path:这是之后我们的图片生成之后的HTTP链接的一级目录的名字
customPath:为了防止图片名重复覆盖,所以需要选择图片的命名方式,这里我选择的是年月的时间戳格式
customUrl:可以不填

## typora

设置启动picgo上传图片

![image-20220326175935265](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202203261800936.png)

点击上传即可

![image-20220326180008724](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202203261800596.png)

# 阿里云oss+typora+picgo

## 阿里云

- 购买阿里云OSS服务

  - > 阿里云OSS服务需要付费购买
    >
    > 这里的价格也不贵
    >
    > 用阿里云付费的也比较稳定可靠

  - 1.首先登录阿里云网站 附上网址:https://www.aliyun.com/

    > 未注册的要先注册后再登录

  - 2.找到[对象存储](https://so.csdn.net/so/search?q=对象存储&spm=1001.2101.3001.7020)点击进入

    > 进入路径：
    >
    > 产品 —> 存储 —> 基础存储服务 —> 对象存储OSS
    >
    > ![image-20220519173515316](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191735214.png)

  - 3.进入界面之后首先需要开通对象存储服务

    > eg：如果已经开通可以跳过这一步

    ![开通服务](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191735672.png)

    *点击立即开通*

    ![立即开通](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191735073.png)

    *在这个界面继续点击立即开通*

    *接下来会弹出确定订单界面，然后打勾就点击确定就行*

    *最终显示这个界面表示开通成功*

    ![开通成功](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191735371.png)

  - 4.创建Buck

    首先需要进入到存储界面的Buck列表

    > 进入可以直接在上图的管理控制台进入
    >
    > 或者点进之前的立即开通也可进入
    >
    > 也可以选择在阿里云首页的控制台进入
    >
    > ![控制台](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191736137.png)

    Buck列表界面是这样的

    ![Buck界面](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191736291.png)

    点击创建Buck

    

    ![image-20220519173814396](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191738350.png)

    ![image-20220519173840741](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191738767.png)

    *读写权限也可以在创建Buck之后继续更改*

  - 5.购买存储包

    依旧是选择在主页选择路径

    产品 —> 存储 —> 基础存储服务 —> 对象存储OSS

    然后下拉选择存储折扣包

    ![存储折扣包](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191742268.png)

    这里我选择的事标准类型的本地冗余ＬＲＳ

    ![选择困难症](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191742438.png)

    常规可以按照按照默认选择，总之根据需求来选择

    然后点击支付就可以了

  - 5.流量包按照默认即可

  - 6.创建用户

    > 单独创建用户来操作OSS

    在管理控制台的对象存储里面选择用户访问控制

    ![image-20220519174246326](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191743021.png)

    按照下图选择创建用户

    主义勾选Open API调用访问

    > 这个后面配置PicGo用到

    ![创建用户](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191742451.png)

    然后点击确定，绑定手机号就可以创建了

    ![KEY2](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191743200.png)

    记住这里的ID和Secret

    后面要用到

  - 7.用户添加权限

    > 使得用户只能访问OSS的服务。点击添加权限，如下图所示：
    >
    > ![添加权限](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191743730.png)
    >
    > ![OSS权限](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191743822.png)
    >
    > 点击确定，点击完成就结束了

## 配置PICGO

> 打开PicGo，没有的需要先下载

![image-20220519174510075](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191745035.png)

其中的ID和Secret就是之前的AccessKey ID，KeySecret

存储空间名就是创建Bucket的名字

存储区域也是创建时设定的，忘记的可以通过Bucket概览查看

![image-20220519174621304](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191746260.png)

其他的并不是必填内容，可以默认

- 8.上传图片

  第一次上传图片显示上传失败

  重启后仍然上传失败

  > 考虑到可能是账户的密码输入不正确,但是又找不到密码可以在哪里查看
  >
  > 于是重新创建了一个用户,设置权限
  >
  > 再次配置完Pic后可以上传
  >
  > 另外,上传的文件内容可以在以下路径查看到
  >
  > ![查看文件](https://img-blog.csdnimg.cn/img_convert/6d0bfd69a6899b03614e518f4a39955d.png)

这时文件就配置完成了!

一个小问题,PicGo可以上传图片,但是typora不能上传

原因是这两边设置的地址不一样

![地址](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191746691.png)

这里显示的是36677,只要把picgo中也改成36677就可以了

![设置sever](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191746785.png)

![36677](https://chenfl-note.oss-cn-hangzhou.aliyuncs.com/note/%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8/typora%E3%80%81picgo%E9%85%8D%E7%BD%AE%E5%9B%BE%E5%BA%8A/img/202205191746003.png)

这样设置之后就可以正常上传了