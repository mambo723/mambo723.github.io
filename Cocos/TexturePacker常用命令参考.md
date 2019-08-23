---
layout:     post                        # 使用的布局(必填)
title:      TexturePacker常用命令参考                     # 标题(必填)
subtitle:   CocosCreator 游戏开发      # 副标题(必填)
date:       2019-08-23                  # 时间(必填)
author:     Mambo723                    # 作者(必填)
gitalk_enable: true                     # 是否开启评论(必填)
---
# TexturePacker常用命令参考
* 一般使用以下标准格式命令行：
  * `TexturePacker [options][<imagefolder>][<images>][<tpsfiles>]`
    * `[options]` 命令行参数，会覆盖tps文件里的参数。
    * `[imagefolder]` 图片目录，递归该目录下所有图片进行打包。
    * `[images]` 也可以指定指定格式的图片。
* 一般就是以下两种格式：
  * `TexturePacker --data main.plist --format cocos2d --sheet file/assetsfolder`
  * `TexturePacker /path/to/your/spritesheet.tps --force-publish`

## 选项说明
* `--help` 帮助
* `--version` 版本
* `--gui` 启动GUI
* `--force-public` 强制发布
* `--replace <regexp>=<string>` 符合正则表达式的用string替代
* `--ignore-files <wildcard>` 忽略一些指定文件，表面重复打包
* `--background-color <rrggbb>` 设置图片背景色
* `--verbose` 全部输出
* `--quiet` 只输出错误

### 设置：
* `--fromat <name>` 数据格式，游戏支持的，一般选cocos2d
* `--data <filename>` 导出的plist，filename包含{n}{n1}，当支持multipack的时候
* `--trim-sprite-names` 移除纹理帧名字的后缀
* `--prepend-folder-name` 加入一个文件目录的名字到每个纹理帧前面
* `--texturepath <path>` 把plist放入到另外的路径下
* `--texture-format <id>` 导出的图片格式，比如png32
* `--sheet <filename>` 导出的大图片，filename包含{n}{n1}，当支持multipack的时候
* `--png-opt-level <value>` png优化级别 级别越高发布的时间越久。
* `--opt <pixelformat>` 像素格式，比如RGBA8888
* `--dither-<mode>` 抖动模式，在较低的像素格式下依然可以有很好的效果。
* `--max-width <int> --max-height <int>` 最大纹理尺寸，默认2048
* `--width <int> --height<int>` 固定大小，若空，则自由尺寸
* `--size-constraints <value>` 尺寸是否采用2的幂
* `--force-squared` 强制为正方形
* `--scale <float>` 缩放
* `--scale-mode <mode>` 缩放模式
* `--algorithm <name>` 合并算法，默认MaxRects
* `--maxrects-heuristics <mode>` 启发式算法，默认Best
* `--pack-mode <mode>` 打包模式，默认Best
* `--multipack` 支持多图打包模式，如sheet{n}.png sheet{n}.plist
* `--enable-rotation --disable-rotation` 是否开启旋转
* `--disable-auto-alias` 自动清除一样的图片
* `--trim-mode <value>` 裁剪模式，裁剪周围透明像素，一般选none，尤其是切换的动画
* `--trim-threshold <int>` 1-255，裁剪低于指定透明值得像素点
* `--border-padding <int>` 与边缘的间隔，默认2
* `--inner-padding <int>` 内部之间的间隔，默认2
* `--shape-debug` 显示测试边框


### 选择 sprite sheets 的两个原因：
1. 减少内存和启动时间。
2. 提高游戏新能。


> ps: 裁剪模式：多边形裁剪PolygonTrim，Polygon mesh sprites 没有透明像素的的绘制，以小幅降低cpu的代价，将大幅提升绘制性能。cocos2dx3.9开始支持，texturepacker4.0.0也开始支持。
> 
>  * 特效精灵：EffectSprite
>  * 灯光特效：LightEffect
>  * 法相贴图：Normal
>  
> example:  `EffectSprite->setEffect(LightEffect, Normal)`