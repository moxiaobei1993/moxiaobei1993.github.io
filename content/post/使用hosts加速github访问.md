+++
title="github访问慢、卡等痛点如何解决"
tags=["网站"]
categories=["网站"]
date="2022-12-13T21:00:00+07:00"
toc=true
+++

# github 访问慢、卡等痛点如何解决

开始一直使用的是 v2ray 代理，但是不可能所有电脑都一直使用这个。综合考虑还是直接使用 hosts 比较方便。

1. github 上直接搜索 github hosts，显示结果如下

   <img src="https://s2.loli.net/2022/12/25/QYSHZfOjUV9AK84.png" alt="image-20221225102232801" style="zoom:50%;" />

2. 选取前面 2 个点赞较高的 hosts 直接替换即可

3. 在 cmd 里通过 ipconfig /flushdns 刷新缓存，直接启用

4. 后续如何快速、···**定时后台**···替换 hosts 和 ipconfig /flushdns，两个仓库都提到[SwitchHosts](https://github.com/oldj/SwitchHosts/releases)这个软件，直接安装使用

###### 存在的问题

github pages 还是无法访问

#### 还有一个有意思的仓库 fetch-github-hosts

<img src="https://s2.loli.net/2022/12/25/fhebTNQCX7F1cHs.png" alt="image-20221225102752808" style="zoom:50%;" />

可以直接替换好多 hosts 源，可以自己添加源，但是需要自己**手动**，符合需求。但是也无法访问 github pages

### 目标未达成

想要折腾这个的初衷是解决了本地的 git push 和 pull 总是连接不到 github 服务器问题

实际上，现在还是连不上。
