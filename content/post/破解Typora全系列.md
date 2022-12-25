+++

title="一劳永逸破解Typora全系列"

tags=["破解"]

categories=["破解"]

date="2022-12-13T21:00:00+08:00"

toc=true

+++

- # 一劳永逸破解 Typora 全系列

第一次看到这个是在 52pojie.cn，https://www.52pojie.cn/forum.php?mod=viewthread&tid=1710146&page=1&authorid=300010，当然，其他博客也有，http://ioohu.com/News/1/487993.html。

文章详细分析了 hook js 注入原理，但是对实操上写的很少，记录下自己的操作过程。

### 重点笔记

```
该方式只要验证逻辑不修改并存在注入点便持续可用，理论多版本兼容。

自动注入工具代码地址：https://github.com/DiamondHunters/NodeInject

破解验证脚本地址：https://github.com/DiamondHunters/NodeInject_Hook_example

使用方式：

下载自动注入工具，用破解脚本替换注入工具中的hooklog.log
构建自动注入工具，放在typora下运行
构建并运行破解验证脚本下的license_gen程序获得激活码
打开Typora，设置使用国内服务器（网络问题，非必须，不开启可能第一次激活失败后会弹出询问是否开启）后填写邮箱（任意）与生成的激活码
激活成功（windows/ubuntu最新版本测试成功）
如果在构建中遇到困难可以在验证脚本的Action中找到预先构建版本
```

### 操作过程

1. 下载 NodeInject 的 github 源代码，这个只是提供了一个框架，

2. 下载 NodeInject_Hook_example 的 github 源代码，这个提供了破解的 hookjs，但是需要第一个框架才能运行。

3. 主要看 NodeInject_Hook_example 的说明。

   ```
   Usage
   git clone https://github.com/DiamondHunters/NodeInject.git
   Replace hooklog.js with hook.js in this repo (or use no_embed feature and set NO_EMBED_HOOK_JS_PATH to the path of hook.js)
   Run cargo build or cargo build --features no_embed to build a type of executable (If you enabled no_embed feature, you need to copy hook.js to the same directory of executable)
   Run the executable
   If you need a fake license, you can run cargo run in license-gen directory

   This fake license is not valid and just used for testing. Please do not use it for any other purpose.
   ```

4. 将 NodeInject_Hook_example 仓库下的 hook.js，替换 NodeInject 仓库的 hooklog.js，要放在相同的位置，并且更名为 hooklog.js。
5. 在当前目录运行 cmd 命令，使用 cargo build 进行编译。这里要安装 rust 才能运行命令。自己百度搜索安装 rust 后成功运行。
6. 运行后，会在 NodeInject 项目下生成一个文件夹，里面有 debug 和一个 exe 文件，exe 文件即为破解软件。
7. 将 exe 移动到 Typora 的安装根目录，根目录打开 cmd，将 exe 拖入，回车，会显示注入完成。
8. 到 NodeInject 目录运行 cmd，运行 cargo run，会显示一个假的秘钥。
9. 打开 Typora，使用假秘钥和任意邮箱即可注册。
