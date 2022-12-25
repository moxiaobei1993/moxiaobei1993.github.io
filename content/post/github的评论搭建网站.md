+++
title="github actions同步github仓库到gitee并自动部署网站"
tags=["网站"]
categories=["网站"]
date="2022-12-13T21:00:00+14:00"
toc=true

+++

# github actions同步github仓库到gitee并自动部署网站





### 存在的问题

上次搭建了github网站，但是github pages国内网络无法访问，想到可以使用gitee pages，但是gitee没有actions，找到教程，可以直接使用github actions。

### 同步github网站到gitee

一篇教你代码同步 Github 和 Gitee

https://github.com/mqyqingfeng/Blog/issues/236



<img src="https://s2.loli.net/2022/12/25/qCIaNwUQZW5AmzS.png" alt="image-20221225225830982" style="zoom:50%;" />

这个可以看自己的网页链接



[如何将github仓库同步到gitee]: https://moxiaobei1993.github.io/blog/post/%E5%A6%82%E4%BD%95%E5%B0%86github%E4%BB%93%E5%BA%93%E5%90%8C%E6%AD%A5%E5%88%B0gitee/	"如何将github仓库同步到gitee"



###  github actions自动部署gitee网站

同步后，每次需要手动更新gitee网站还是比较麻烦

Gitee 如何自动部署 Pages？还是用 GitHub Actions!

https://github.com/mqyqingfeng/Blog/issues/238

1. gitee发布网站，需要实名认证。身份证正反，并且手持身份证正反照片。
2. 
