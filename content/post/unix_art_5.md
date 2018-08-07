---
title: "Unix编程艺术(五)"
date: 2018-08-06T15:00:31+08:00
categories: [UNIX]
tags: [2018-8]
TableOfContents: true
---

# Unix配置

> 经验法则：<hr />

> + 提高适应能力，除非这样做会产生超过0.7秒的延迟。0.7秒是一个魔数，人们几乎察觉不到少于0.7秒的启动延迟。
> + 用户不应该看到优化开关。让程序经济运行时设计者的任务，不是用户的任务。与提高界面复杂度成本相比，让用户从优化开关来获取那点儿性能收益，换来界面复杂度的提升，往往得不偿失。
> + 能用脚本包装器或简单管道实现的任务，就不要用配置开关实现。能简单利用其它程序来完成的任务，就不要增加本程序的复杂度。

## 

> 增加配置是要考虑的问题：<hr />

> + 能省掉这个功能吗？为什么在加厚手册之外还要加重用户负担？
> + 能否用某种无伤大雅的方式改变程序的常规行为从而无需这个选项？
> + 这个选项是否花哨没用？是否应该少考虑用户界面的可配置性而多考虑正确性？
> + 这个选项附加的行为是否应该用一个独立的程序来代替？
>
> <hr />增加不必要的选项会产生很多不良后果。其中最严重的后果是对测试覆盖率的影响。

## unix命令行选项

|&nbsp;&nbsp;参数|说明|
|---|---|
|-a| all : 全部，所有 (ls , lsattr , uname) archive : 存档 (cp , rsync) append : 附加 (tar -A , 7z) |
|-b| blocksize : 块大小，带参数 (du , df) batch : 批处理模式 (交互模式的程序通常拥有此选项，如 top -b)|
|-c| commands : 执行命令，带参数 (bash , ksh , python) create : 创建 (tar)|
|-d| debug : 调试 delete : 删除 directory : 目录 (ls)|
|-e| execute : 执行，带参数 (xterm , perl) edit : 编辑 exclude : 排除|
|-f| force : 强制，不经确认(cp , rm ,mv) file : 文件，带参数 (tar) configuration file : 指定配置文件(有些守护进程拥有此选项，如 ssh , lighttpd)|
|-g||
|-h| --help : 帮助 human readable : 人性化显示(ls , du , df) headers : 头部|
|-i| interactive : 交互模式，提示(rm , mv) include : 包含|
|-k| keep : 保留 kill|
|-l| long listing format : 长格式(ls) list : 列表 load : 读取 (gcc , emacs)|
|-m| message : 消息 (cvs) manual : 手册 (whereis) create home : 创建 home 目录 (usermod , useradd)|
|-n| number : 行号、编号 (cat , head , tail , pstree , lspci) no : (useradd , make)|
|-o| output : 输出 (cc , sort) options : 选项 (mount)|
|-p| port : 端口，带参数 (很多网络工具拥有此选项，如 ssh , lftp ) protocol : 协议，带参数 passwd : 密码，带参数|
|-q| quiet : 静默|
|-r| reverse : 反转 recursive : 递归 (cp , rm , chmod -R)|
|-s| silent : 安静 size : 大小，带参数 subject|
|-t| tag type : 类型 (mount)|
|-u| user : 用户名、UID，带参数|
|-v| verbose : 冗长 version : 版本|
|-w| width : 宽度 warning : 警告|
|-x| exclude : 排除 (tar , zip)|
|-y| yes|
|-z| zip : 启用压缩 (bzip , tar , zcat , zip , cvs)|