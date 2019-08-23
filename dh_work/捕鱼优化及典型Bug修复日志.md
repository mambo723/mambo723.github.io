---
layout:     post                        # 使用的布局(必填)
title:      捕鱼优化及典型Bug修复日志                     # 标题(必填)
subtitle:   工作日志            # 副标题(必填)
date:       2019-08-23                  # 时间(必填)
author:     Mambo723                    # 作者(必填)
gitalk_enable: false                     # 是否开启评论(必填)
---


> 赠人玫瑰，手有余香。


# 捕鱼优化及典型Bug修复日志
## 优化日志


> 记录相关优化


1.  由于Spine动画DC无法合并，且序列帧动画内存不经济，所以鱼根据出现的数量，会大量出现的使用序列帧动画，不会大量出现但需要复杂表现的使用Spine动画。

2.  最初鱼的位置与姿态采用贝塞尔曲线算法实时运算，但实时运算会占用一定的性能消耗，特别在IPhoneX这种机型上提现的尤为明显，故采用提前预运算的方法，在开发阶段将所有的点都计算出来，运行时只作插值和角度运算。

3.  为减轻性能压力，出鱼时采用分帧加载的方式，减轻每帧的压力，改善卡帧现象。

4.  开发初期使用的资源，包括UI切图和Spine资源不合理，导致内存占用过大，后期优化尽量减小图片尺寸，尽可能使用九宫格和对称切图，spine动画的图集尽可能保持矩形，减小图片尺寸能有效降低其在内存中展开的大小。

5.  开发初期使用的Creator版本为2.0.9，但这个版本在原生平台的内存占用十分异常，为理论内存占用的两倍，经调查原因是这个版本在原生平台默认关闭了 cc.macro.CLEANUP_IMAGE_CACHE，cc.macro.CLEANUP_IMAGE_CACHE表示[是否在将贴图上传至 GPU 之后删除 DOM Image 缓存](https://docs.cocos.com/creator/api/zh/classes/macro.html#cleanupimagecache)，所以内存占用异常的高，开启之后有极大的改善，好在[2.0.10版本官方已经做出了修改](https://forum.cocos.com/t/cocos-creator-v2-0-10-05-26-rc-5/77723?u=1712655110)。

6.  CocosCreator v2.0.10及之后的版本引入了tween.js，支持使用灵活性更高的cc.tween创建节点动作，提高开发效率。但使用ts开发时由于creator.d.ts中未定义tween，无法直接访问cc.tween，捕鱼在CCFHelp.ts中定义了tween的get方法，使用CCFHelp.tween就可以使用，但没有代码提示，需要查阅[文档](https://docs.cocos.com/creator/2.1/api/zh/classes/Tween.html?h=tween)使用。另外需要注意的是，v2.0.10版本中tween存在问题，[cc.tween 的值为 0 时可能无效果](https://forum.cocos.com/t/2-1-1-cc-tween-bug-2-1-2/80081)，所以v2.0.10版本不建议使用tween。[此问题已在v2.1.2版本中解决](https://github.com/cocos-creator/engine/pull/4790)，新版本中可以使用。

7.  关于触摸气泡效果的实现：效果本身使用 CocosCreator 的 Animation 组件和粒子动画实现，这方面比较简单，真正有趣点在于 CocosCreator 的事件系统。CocosCreator 的触摸事件传递使用的是[节点树冒泡传递](https://docs.cocos.com/creator/manual/zh/scripting/internal-events.html#%E8%A7%A6%E6%91%B8%E4%BA%8B%E4%BB%B6%E5%86%92%E6%B3%A1)，也就是由子节点一层一层向父节点传递。如果对事件调用 `event.stopPropagation()` 则会阻断冒泡过程，比如引擎中[cc.Button](https://github.com/cocos-creator/engine/blob/8bf4522a6d43b53258219983aabd728909ce24ca/cocos2d/core/components/CCButton.js#L650)、[cc.ScrollView](https://github.com/cocos-creator/engine/blob/8bf4522a6d43b53258219983aabd728909ce24ca/cocos2d/core/components/CCScrollView.js#L950)、[cc.BlockInputEvents ](https://github.com/cocos-creator/engine/blob/8bf4522a6d43b53258219983aabd728909ce24ca/cocos2d/core/components/CCBlockInputEvents.js#L32)等一些组件中就会有这个操作，这就导致在根节点监听触摸事件的时候，如果触摸事件是由这些组件触发，触摸事件将不会冒泡传递到根节点，这就造成了点击挂载有这些组件的节点时不能触发效果。解决这个问题需要手动将触摸事件分发给根节点，具体做法是在挂载有这些组件的节点上监听触摸事件，将接收到的触摸事件原样分发给目标节点。这里需要注意的是，事件分发有两种方式，分别是 [emit](https://docs.cocos.com/creator/manual/zh/scripting/events.html#%E5%8F%91%E5%B0%84%E4%BA%8B%E4%BB%B6) 和 [dispatchEvent](https://docs.cocos.com/creator/manual/zh/scripting/events.html#%E6%B4%BE%E9%80%81%E4%BA%8B%E4%BB%B6)，使用 dispatchEvent 分发会影响事件的冒泡过程，导致事件的 target 属性发生变化，target 会变成 dispatchEvent 的目标节点，如果在正常的冒泡过程中使用了 target 属性就会产生问题，所以此时不能使用 dispatchEvent，应该使用 emit 进行分发。

##  典型Bug修复日志

> 记录典型Bug修复的过程


1.  在程序中调用强制垃圾回收会导致卡顿，所以将原来出鱼阵时的垃圾回收去掉，但保留换桌时的垃圾回收，解决出鱼阵时卡顿的问题。

2.  IPhone6、IPhoneX等机型运行游戏时发热严重的问题，[是由于ios的安全策略限制，不能开启 js jit，导致重逻辑的模块性能低下](https://forum.cocos.com/t/topic/77986/21?u=1712655110)。目前没有可靠的方法解决。

3.  Spine动画本身是支持使用SkeletonBounds进行碰撞检测的，[但Creator引擎官方并未支持在原生平台使用SkeletonBounds](https://forum.cocos.com/t/spine/43312/4?u=1712655110)，有官方人员说会添加，不过至今没有任何回应。

4.  在CocosCreator中，removeFromParent方法执行的操作是从从父节点上移除这个节点，执行完后会立即从父节点上移除，最终会销毁节点，但并不会触发onDestroy回调，执行removeFromParent操作之后可以再为节点指定一个新的父节点。destroy调用后不会立即从父节点上移除，而是在当前帧结束的时候移除并销毁节点，并会触发onDestroy回调，操作之后不能再为节点指定一个新的父节点。所以使用removeFromParent移除节点是常常会因不会触发onDestroy回调造成许多问题，所以[如果一个节点不再使用，destroy 就对了，不需要 removeFromParent 也不需要设置 parent 为 null](https://docs.cocos.com/creator/manual/zh/scripting/create-destroy.html#destroy-%E5%92%8C-removefromparent-%E7%9A%84%E5%8C%BA%E5%88%AB)。[*参考资料1*](https://forum.cocos.com/t/removefromparent-destroy/38396/10?u=1712655110)，[*参考资料2*](https://forum.cocos.com/t/removefromparent-destroy/38396/14?u=1712655110)。

5.  在使用事件系统时，一般会在组件的onDestroy回调中使用targetOff方法注销监听，所以监听是注意全部加上target参数，以确保所有事件全部注销监听，避免出现重复监听的错误。在不继承component的类中使用事件监听，也要注意在合适的地方注销。

6.  平时开发调试的过程中，开启模拟器调试界面中的：Pause on exception，该功能能够在程序发生异常的时候自动在发生异常处断点，并完好的保留错误现场。保持这个功能的开启能够有效提升开发效率和质量。


---

# **感谢分享**