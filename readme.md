# xmake使用笔记

## 前言

由于受够了cmake以及bazel，现在我开始尝试使用xmake来管理自己所有的C++项目以及代码:)

所有的环境均默认使用gcc+clang的编译器组合，出于个人习惯，编辑器以及代码跳转使用vscode + clangd的模式。

推荐如下的xmake文档：
https://zhuanlan.zhihu.com/p/640701847

## 安装xmake

参考官方文档进行xmake的安装

```
wget https://xmake.io/shget.text -O - | bash
```

检查xmake版本

```
xmake --version
```

如果正常会出现如下输出

```
xmake --version
xmake v2.8.1+20230806, A cross-platform build utility based on Lua
Copyright (C) 2015-present Ruki Wang, tboox.org, xmake.io
                         _
    __  ___ __  __  __ _| | ______
    \ \/ / |  \/  |/ _  | |/ / __ \
     >  <  | \__/ | /_| |   <  ___/
    /_/\_\_|_|  |_|\__ \|_|\_\____|
                         by ruki, xmake.io
    
    👉  Manual: https://xmake.io/#/getting_started
    🙏  Donate: https://xmake.io/#/sponsor
```

## 创建xmake示例工程

```
xmake create hello
```

会出现如下

```bash
create hello ...
  [+]: src/main.cpp
  [+]: xmake.lua
  [+]: .gitignore
create ok!
```

这时代码根目录下会出现一个lua脚本，并指明了这个示例项目的构建target：hello

## 一些xmake命令

如果使用vscode可以直接安装xmake插件，免去敲繁琐的命令。

* 构建项目并运行
```
xmake
# xmake build hello
xmake run
```

* 切换编译工具链

一般来说windows下都使用msvc，linux和mac使用gcc和clang（严格来说mac下只有clang，mac下gcc是硬链接向clang），不过建议windows下使用wsl以便对齐linux下的使用体验。

```
xmake f --toolchain=clang #切换为clang
```

* 导出cmakelists以支持cmake构建
  
```
xmake project -k cmakelists
```

* 切换构建类型，常见的都是debug和release

```
xmake f -m debug
# 或者
xmake config --mode=debug
```

## 包管理

这里只是简单粘贴一下xmake管理第三方依赖的一些语法，个人并不是太习惯使用构建工具去管理第三方依赖（毕竟公司里搬砖都是从对象存储上拖三方库，有自己的一套管理脚本）。而且因为网络原因，很可能三方库下载失败，我更情愿自己手动管理所有的三方依赖库。

```
add_rules("mode.debug", "mode.release")

add_requires("fmt")

target("hello")
    set_kind("binary")
    add_files("src/*.cpp")
    add_packages("fmt")
```

只需要引入***add_requires***以及***add_packages***，

再执行

```
xmake f -y
xmake
```

即可拉取依赖。

## 关于xmake.lua

xmake.lua之于xmake类似cmakelists.txt之于cmake，但xmake使用了lua，没有cmake那样的晦涩艰深。

