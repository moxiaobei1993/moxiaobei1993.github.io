+++
title="如何将github仓库同步到gitee"
tags=["网站"]
categories=["网站"]
date="2022-12-13T21:00:00+09:00"
toc=true

+++

# 如何将 github 仓库同步到 gitee

##### 看的教程地址：

一篇教你代码同步 Github 和 Gitee
https://github.com/mqyqingfeng/Blog/issues/236

按照教程做的，但是无法运行，自己的 yml 代码如下

```yml
name: syncToGitee
on:
  push:
    branches:
      - gh-pages
jobs:
  repo-sync:
    runs-on: ubuntu-latest
    steps:
      - name: Mirror the Github organization repos to Gitee.
        uses: Yikun/hub-mirror-action@master
        with:
          src: 'github/moxiaobei1993'
          dst: 'gitee/xiaobei'
          dst_key: ${{ secrets.GITEE_PRIVATE_KEY }}
          dst_token:  ${{ secrets.GITEE_TOKEN }}
          static_list: "blog"
          force_update: true
		  debug: true
```

作者本身提出了几个注意事项

```
我们也可以利用 Github Actions，写一个工作流，在发现 Github 博客仓库的 gh-pages 分支代码更新后，自动同步当前代码到 Gitee 上。
当我们把这样的文件提交到 Github，Github 会自动检测并运行该脚本。但是现在还有几个问题要注意：

1--因为我们是提交到 Github 的 gh-pages 分支上，这个文件和目录需要写在 gh-pages 分支
2--观察我们的脚本代码，我们就会发现，每次我们 sh deploy.sh 的时候，都是编译代码到 dist 目录，然后重新 git init ，最后强制提交。所以我们在项目的根目录建立 .github/woorkflows/syncToGitee.yml 并没有什么用，一来提交的是 dist 目录里的代码，二是每次还都会清空重新编译生成代码提交。
```

######这里自己理解是：

syncToGitee.yml 文件放到 gh-pages 分支的.github/workflows/目录下，这样当 gh-pages 分支有 push 的时候，yml 才会执行；第二点就是在自己电脑上的 yml 文件会被清空，所以要在其他位置放一个 yml 文件，然后推送到 gh-pages 分支的.github/workflows/目录下，这样就能保证 gh-pages 上每次都有 yml 文件。

######自己的问题：

自己也是放到 gh-pages 分支的.github/workflows/目录下，但是每次 github pages 会清空这个目录，导致无法运行。

###### 解决方法

1. 现在 main 分支试一下，不要动 gh-pages 分支
2. 看如何让 pages 不清空.github/workflows/目录

###### 测试结果

1. 直接部署在 main 分支，还是不行，错误如下：

   ```
   (1/3) Downloading...
   Starting git clone https://github.com/moxiaobei1993/blog.git
   Clone completed: /github/workspace/github/workspace/hub-mirror-cache/blog
   (2/3) Creating...
   Warning: destination repos is []
   blog doesn't exist, create it...
   Destination repo creating failed: {"error":{"base":["已存在同地址仓库（忽略大小写）"]}}
   (3/3) Force pushing...
   Remote exsits, re-create: set gitee to git@gitee.com:xiaobei/blog.git
   (3/3) Force pushing...
   Remote exsits, re-create: set gitee to git@gitee.com:xiaobei/blog.git
   (3/3) Force pushing...
   Cmd('git') failed due to: exit code(128)
     cmdline: git push -f gitee refs/remotes/origin/*:refs/heads/* --tags --prune
     stderr: '[session-834493be] Auth error: 404 not found!
   fatal: Could not read from remote repository.

   Please make sure you have the correct access rights
   and the repository exists.'
   ```

   目的仓库是[]，后面又说存在同名仓库，强制 push，后面有个 Auth error，最后总结：是否有权限，仓库是否存在。

   #####问题解析：

   应该就是代码存在错误。

   自己查看https://github.com/Yikun/hub-mirror-action这个仓库的action使用方法吧。

   1. 重新添加 secret，因为可能之前存在错误。
   2. 添加部分参数

```
# 工作流的名称
name: syncToGitee
# 触发事件
on:
  # 当你向分支 gh-pages 推送了代码时触发此工作流
  push:
    branches:
      - main

jobs:
  #工作名称
  repo-sync:
    runs-on: ubuntu-latest
    steps:
      - name: Mirror the Github organization repos to Gitee.
        uses: Yikun/hub-mirror-action@master
        with:
          src: 'github/moxiaobei1993'
          dst: 'gitee/xiaobei'
          dst_key: ${{ secrets.GITEE_PRIVATE_KEY }}
          dst_token:  ${{ secrets.GITEE_TOKEN }}
          mappings: "blog=>blog"
          white_list: "blog"
          static_list: "blog"
          force_update: true
		      debug: true
```

3. 显示成功了，但是 gitee 仓库中没有代码，看下详细的 log，代码错了，应该是 white_list，写成了 black_list，所以直接跳过了。现在改回来。

4. 但是还是一样的错误，删除 gitee 仓库中的 readme 看下。

5. 还是不行，直接删除仓库看下。

6. 删除仓库后，脚本会创建仓库，但是无法同步，不知道为什么

7. 再次看https://github.com/Yikun/hub-mirror-action这个仓库的说明，里面有提到别人的3篇教程，仔细看下这3篇教程。

8. 这个教程https://github.com/yi-Xu-0100/hub-mirror提到一个名字的问题，我不是xiaobei，是villege，试一下看。

9. 成功了，就是名字的问题，在 gitee 上一直用的是自己的用户名，而不是 github 工作台的名字，直接也将 gitee 的工作台名字改成 moxiaobei1993 了。

   ### 总结

   自己一直看一个教程，导致没法发现问题所在，要兼听则明，看原始文档，看多个教程，综合来评判，因为一个人关注的角度不同。

10. 现在直接修改自己 gitee 的名字也为 moxiaobei1993，再开始自己的推送部署了。完全成功，但是不能启用推送到 gh-pages 触发同步（因为这个要放到 gh-pages 分支才能触发，但是 gh-pages 分支每次都会在部署 pages 的时候清空），只能推送到 main 分支触发，刚好可以放在博客部署的任务一起，当做一个流程。
