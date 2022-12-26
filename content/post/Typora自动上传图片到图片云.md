---
title: "Typora自动上传图片到图片云"
date: 2022-12-14T10:33:19+08:00
draft: true
---

###Typora 自动上传图片到图片云(七牛云、smms)

使用hugo写md博客，但是一直存在一个问题，就是图片上传比较麻烦，有些图片也比较大，上传github比较慢，之前了解到，可以使用图床服务，但是每次写文章还要上传，然后复制链接，比较麻烦，能不能有一种方法，直接帮你上传了，你啥都不用管的。

#### typora加python脚本上传到七牛云

源自看到52pojie论坛上的一篇文章，利用python脚本上传本地图片到七牛云，并且自动生成图片云链接，自己不需要任何操作。

[Python 原创] Typora 快速上传图片到七牛云

https://www.52pojie.cn/forum.php?mod=viewthread&tid=1714755&extra=&highlight=Typora&page=1


1. 核心内容

   Typora  快速上传图片到七牛云
   将图片上传到七牛云对象存储中（访问速度快），免费10G

   使用方法
   将此代码解压放入Typora根目录
   打开代码修改accesskey 与 secretkey（需注册七牛云账号）
   还有self.bucket_name 
   还有 # 拼接完整url地址
               full_url = "这里改成自己的地址"
   打开Typora - 文件 - 偏好设置 - 图像
   <img src="https://s2.loli.net/2022/12/26/5YPlWC3goVSmBRp.png" alt="image-20221226104324389" style="zoom:50%;" />
   按照上述图片填入

2. 他这里有几个问题，一是python里面的accesskey 与 secretkey、self.bucket_name、  full_url = "这里改成自己的地址" 应该要修改4个地方，教程只让修改了2个地方，导致一直尝试不成功，自己用python调试才解决这个问题。二是图片上命令的问题，使用的是 python ‘./qiniu_upload.py’，这里是不准确的，要使用完整的路径。三是使用了这个脚本后，图片会变成一个链接，不能形成图片形式显示，好像是typora自己没法下载预览图。四是七牛云的注册和配置比较复杂，自己以为注册一个账号就行了，其实要开通图片空间。

3. 注册七牛云并开通图片空间:

   - 注册七牛云
   - 实名认证，要手机人脸识别。
   - 对象存储Kodo--空间管理--新建空间，这里只能选择国外的，因为国内需要域名备案，我的域名都是国外免费域名，无法备案，所以只能用国外不需要备案域名的空间。这里选择的是新加坡（每个区域有代号，需要添加到脚本里的，自己百度它的代号即可）。
   - 生成空间后，会给免费一个月的域名，一个月后到期无法使用，这里直接配置自己的永久域名（之前白嫖的永久无需手动点击续期域名moxiaobei.eu.org），配置的是qiniu.moxiaobei.eu.org，配置方式可以自己百度更加详细：点击进入空间--域名管理--自定义 CDN 加速域名--绑定域名--后面按照百度的操作即可。
   - 绑定域名后，一直显示在部署，但是其实15分钟左右后就可以直接使用了，可以再自定义一个域名：点击进入空间--域名管理--自定义源站域名--绑定域名img.moxiaobei.eu.org--立马生效。
   - 然后需要：进入空间--文件管理--外链域名--选择img.moxiaobei.eu.org即可

   #### picgo自动上传到七牛云、smms及其他好多图片云

   看帖子的评论，发现picgo更系统，typora也是原生支持picgo，下面进行调试一下。

![image-20221226110517328](https://s2.loli.net/2022/12/26/tyW4s6uNmBLCd38.png)

1. 下载picgo.exe文件，安装后，配置好七牛云和smms，直接使用。
2. 配置到typora，可以使用，但是每次都会启动picgo软件。
3. 使用picgo-core，直接github或者typora下载picgo-core软件，直接配置config文件即可。
4. picgo.exe 上有个配置文件，直接打开，复制后，配置到picgo-core上即可，七牛云可以直接使用，但是smms总是错误。

![image-20221226110814205](https://s2.loli.net/2022/12/26/IENsR8umiJKH2q9.png)

```
{
  "picBed": {
    "current": "smms-user",
    "uploader": "smms-user",
    "smms-user": {
      "Authorization": "自己填写",
      "backupDomain": "smms.app"
    },
    "qiniu": {
      "accessKey": "自己填写",
      "secretKey": "自己填写",
      "bucket": "tusp2023",
      "url": "http://img.moxiaobei.eu.org",
      "area": "as0",
      "options": "",
      "path": "picgo/"
    }
  },
  "settings": {
    "shortKey": {
      "picgo:upload": {
        "enable": true,
        "key": "CommandOrControl+Shift+P",
        "name": "upload",
        "label": "QUICK_UPLOAD"
      }
    },
    "showUpdateTip": false,
    "privacyEnsure": true,
    "pasteStyle": "markdown",
    "server": {
      "port": 36677,
      "host": "127.0.0.1",
      "enable": true
    }
  },
  "needReload": false,
  "picgoPlugins": {
    "picgo-plugin-smms-user": true
  },
  "debug": true
}
```

5. 经过多次配置，和查看其他人的教程，发现问题如下：

   ```
   "picBed": {
       "current": "smms-user",
       "uploader": "smms-user",
       "smms-user": {
         "Authorization": "自己填写",
         "backupDomain": "smms.app"
       },
   ```

   之前是 smms ，不行，要使用  smms-user

   之前是verify， 不行， 要使用 Authorization

   之前没有backupDomain，不行，一定要添加"backupDomain": "smms.app"，因为sm.ms服务器国内无法使用，smms.app服务器才可以使用。

   #### 七牛云和smms

   七牛云：免费10G空间，配置麻烦，需要配置自己的域名，但是自己域名链接好看。

   smms：免费5G空间，单文件不超过5M，无自己域名。**可以在typora上预览图片**。

   综上，还是选择smms。

   当然，也有其他图服务，比如imgur，但是没必要折腾了。

