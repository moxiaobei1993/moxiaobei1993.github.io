+++
title="hugo搭建github网站并使用actions自动部署"
tags=["网站"]
categories=["网站"]
date="2022-12-13T21:00:00+11:00"
toc=true
+++

# hugo 搭建 github 网站并使用 actions 自动部署



最开始有这个想法，就是看了b站这个视频:

【北屿】GitHub Actions+GitHub Pages免费搭建个人博客https://www.bilibili.com/video/BV1ua411B7WH

里面提到github actions可以做很多事情，搭建博客、云相册、搭建资源共享网站等。当时就想搞一个博客，记录下折腾过程。

### 推荐github pages + hugo静态网站

优点如下

<img src="https://s2.loli.net/2022/12/25/rW45wHCT2oqZXMA.png" alt="image-20221225220036215" style="zoom:50%;" />

【北屿】的文章只是引起了我的兴趣，但是真正搭建网站，他的这个教程并不完整，自己百度搜索了一些好的教程跟着做。

### 搭建本地hugo网站

找到了一个更好的hugo网站搭建教程

【教程】使用Hugo建立静态网站教程（基础）--LaymanIT

https://www.bilibili.com/video/BV1m4411c7ia

1. 直接访问gohugo.io，安装windows系统 amd64 的hugo.exe文件，放在全英文路径即可。

2. 到系统环境变量的path设置该目录为环境变量，就可以在任何目录直接cmd运行hugo，cmd运行 hugo version检查是否设置好

3. 安装git软件，这个后面用到，可以先安装

4. hugo new site blog，新建名为blog的站点

5. 到themes.gohugo.io选择一个主题下载，或者到github上直接搜索相关主题。下载后放到blog/themes文件夹下，目前使用的是maupassant，https://github.com/pagecho/maupassant，可能不对，也没有再找，自己看相关的内容，可以修改主题。

6. 使用主题，到blog\themes\maupassant\exampleSite目录下，将内容复制到blog目录下，即启用了例子网站，修改blog下的config.toml文件，添加theme = "maupassant"，启用主题。

7. 本地运行网站，到blog目录运行cmd，hugo server命令，可以看到，http://localhost:1313/网站已经启动，浏览器输入即可访问本地。

8. 后续修改config.toml和blog\themes\maupassant\里面的文件，可以修改主题样式，详细如何修改，看主题的官网。

9. 为网站添加内容 **hugo new post/文章名字可以中文.md**  有的主题是posts，还是要看主题的readme文件

10. 生成静态网页，hugo -D，生成的文件都放在./public/文件夹下.后面将这个文件夹里的内容上传到GitHub即可使用github pages生成网页。

    

    ### 将网站部署到github上

    这个换一个视频，但是没有声音，这里需要学习git知识，当然只看这个也是可以的。

    https://www.bilibili.com/video/BV1WA411h76h/?vd_source=13fa6902c1cd1c2e5604dbe55d8b3914

1. 在public目录，git init，讲网站初始化为github项目
2. git add .  或者 git add -A  将网站所有文件添加到缓存
3. git commit -am'init'   提交到本地git上，备注是init
4. git remote add origin https://github.com/moxiaobei1993/仓库名字.git， git连接到仓库，可能会弹出登录github的页面，直接登录即可。
5. git push -f origin master , 强制第一次推送到master分支。-f为强制推送。后面如果有修改的话，直接git push就行。



### 存在的问题

每次修改网页后，需要hugo -D生成public的静态网页，再git push上传public文件夹，比较麻烦。

### 使用github actions自动部署hugo网站

其实可以搜索很多教程，我使用的是下面教程里面的actions模板

【北屿】GitHub Actions+GitHub Pages免费搭建个人博客https://www.bilibili.com/video/BV1ua411B7WH

这个模板其实也一般，但是也能满足要求，用了就没有再折腾修改了。

直接fork这个模板https://github.com/awesome-actions-template/hugo-papermod-template，然后删除除.github/workflows/main.yml外的所有内容，只需要使用main.yml文件的自动化动作。

```
# 工作流的名称
name: hugo-deploy

# 触发事件
on:
  # 当你向主分支 main 推送了代码时触发此工作流
  push:
    paths-ignore:
      - "images/**"
      - "LICENSE"
      - "README.md"
    branches:
      - main
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Git checkout
        uses: actions/checkout@v2
        with:
          ref: main
          submodules: true
          fetch_depth: 1

      - name: Get Theme
        run: git submodule update --init --recursive

      - name: Update theme to Latest commit
        run: git submodule update --remote --merge

      - name: Setup hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: "0.83.0"

      - name: Build
        run: hugo --buildDrafts --gc --verbose --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

后面将自己的网站源代码，就是blog目录上传到mian分支， action会自动生成public并部署到gh-pages分支，直接访问https://moxiaobei1993.github.io/blog/ 就可以访问了。

以后只要main分支有任何修改，actions都会自动启动，并推送更新的到gh-pages分支，网站也就随之更新。

注意：这里的更新使用了一部分github pages的bot，所以会清空gh-pages分支的内容，之前想在gh-pages分支放yml文件，是不行的，会被清空。
