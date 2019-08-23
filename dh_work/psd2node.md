---
layout:     post                        # 使用的布局(必填)
title:      psd2node安装使用方法                     # 标题(必填)
subtitle:   工作日志            # 副标题(必填)
date:       2019-08-23                  # 时间(必填)
author:     Mambo723                    # 作者(必填)
gitalk_enable: false                     # 是否开启评论(必填)
---
## psd2node安装使用方法
---
* [插件路径](svn://192.168.1.242/H5_client/branches/game_dev/devTool/psd2node)
* 将插件整包放到C:\Users\Administrator\\.CocosCreator\packages\全局目录下面
* 重启编辑器，菜单栏可以看到packages，点击进入子菜单psd2node，打开面板。
* 首先需要输入正确的psd文件路径，目录中含/和\都可以。
* 点击生成JSON可以查看psd文件结构，有生成成功日志显示在控制台，这一步是可选的。
* 然后输入切图导出目录，所填内容应是assets/resources目录下的子路径，格式默认为PNG。
* 重启编辑器，以便生成PNG的meta文件。
* 点击开始转换，就可以在主场景的canvas下看到转换过来的node。
* 如果需要预置，可以自行将node拖入到资源管理器即可。
* 当前只支持图片、文字和按钮三个控件，暂不支持其他控件。
* 当前字体默认使用 db://assets/res/common/fonts/fzzy_GBK.TTF
* 当前版本是1.0，还有许多不完善的地方，欢迎指正及时完善。