---
layout:     post                        # 使用的布局(必填)
title:      CocosCreator 常见问题                     # 标题(必填)
subtitle:   CocosCreator 游戏开发      # 副标题(必填)
date:       2019-08-23                  # 时间(必填)
author:     Mambo723                    # 作者(必填)
gitalk_enable: true                     # 是否开启评论(必填)
---
# CocosCreator 常见问题
## 业务相关
### 1. CocosCreator 和 cocos2d-x 的区别
* 实现不同，Creator 引擎是采用 cocos2d-x-lite + jsb 和 js engine 分别实现原生和h5平台
* cocos2d-x-lite 是基于cocos2d-x 3.9 的简化版，2d-x基本属于代码驱动
* Creator优化了相关工具链，提高了开发效率,
* cocos creator是一套包含编辑器在内的开发框架，其内部引擎使用了cocos2d-x js的精简修改版本
* cocos2d-x 资源使用了引用计数进行管理，Creator 因为 js 的自动垃圾回收无法使用引用计数

---

### 2. CocosCreator如何实现多平台发布的
* 原生平台使用 cocos2d-x-lite + jsb ，h5平台使用js engine运行于浏览器环境

---

### 3. Creator中需要动态载入的资源，放在工程的哪个子目录中 
* resources

---

### 4. 资源动态加载和非动态加载的区别
* 使用 cc.loader 加载的属于动态加载，直接在场景上拖上去的属于非动态加载
* 非动态加载资源参与场景销毁时的资源自动释放，动态加载不会，需要手动释放资源
* 需要动态载入的资源，必须放在工程的resources目录中 

---

### 5. 释放资源时要注意哪些问题
* 需要注意被释放的资源是否被引用，否则会渲染出错

---

### 6. 生命周期回调有哪些，触发的时机
*  onLoad   组件首次激活时触发
*  start    组件第一次执行 update 之前触发
*  update     每一帧渲染前调用
*  lateUpdate 所有组件 update 调用后调用
*  onDestroy  组件或所在节点调用了 destroy() 时调用，并在当前帧结束时统一回收组件
*  onEnable  组件的enabled属性从 false 变为 true 时
*  onDisable  组件的 enabled 属性从 true 变为 false 时

---

### 7. Creator对齐UI使用什么组件
* widget 组件

---

### 8. Creator UI 屏幕适配有哪些心得
* widget 组件能有效帮助屏幕适配
* 非异型屏幕的适配只需要合理使用 widget 组件将 UI 元素贴边摆放
* 异型屏幕的适配要点在于获取适配偏移值 offset ，然后使用 offset 动态的设置 widget 组件的 up 、down 、 left 、 right 的值，然后执行对其操作即可

---

### 9. 游戏性能指标有哪些
* 内存占用
* CPU占用
* Draw Call
* FPS

---

### 10. draw call 如何优化，如何合并
* 合并 draw call 的规则是这样的：
  * render command 相邻，大致可以理解为节点树中顺序相邻
  * texture 一致
  * blend function 一致
  * shader program 一致
  * label 会打断drawcall合并，所以尽量使可以合并的节点相邻，避免 label 在中间打断，或使用 BitMap 字体

---

### 11. spine动画能否合并draw call
* 不能

---

### 12. 被Mask组件裁切不在显示区域内的节点会占用 draw call 吗
* 会

---

### 13. 如何优化ScrollView
* 隐藏显示区域外的节点，active 为 false 的节点不占用 draw call
* 复用列表项，根据 content 移动的位置动态的更新内容
* 使用对象池管理列表项
* 参照 draw call 的合并规则，合理规划列表项中的渲染节点

---

### 14. 如何进行资源和内存优化
* 图片压缩有利于减小包体大小，但不能减小内存占用
* 缩小图片尺寸能减少内存占用
* 合理使用图集能够合并 draw call
  * 引擎提供的动态图集能够在运行时实时合图集
  * 编辑器提供自动图集功能，但合图的算法不如 TexturePacker 好
* 动画方面权衡序列帧和骨骼动画的使用
  * 序列帧动画有利于 draw call 合并但内存不经济
  * 骨骼动画内存经济且美术效果好，但不能合并 draw call
* 使用合理的策略进行资源的加载和释放
  * 资源规划时必须各个模块使用的资源隔离开，避免释放了被引用的资源，公共部分放在一起
  * 将不需要用到的资源释放
* 合理使用过渡场景避免内存峰值

---

### 15. 一般而言造成FPS低的直接原因是什么，如何优化FPS
* 原因是主循环中游戏逻辑占用太多时间
  * 比如大量频繁的创建和销毁节点
  * 大量频繁的加载和释放资源
  * 过多的逻辑操作运算等
* 优化方法是压缩运算量、采用分帧操作避免卡帧，使用对象池

---

### 16. 使用全局单例要注意哪些问题
* 需要格外注意释放

---

### 17. 逻辑开发如何解耦
* 合理使用事件系统有助于逻辑解耦

---

### 18. a节点和b节点非父子关系，如何获取a相对b节点的坐标

```
b.convertToNodeSpaceAR(a.convertToWorldSpaceAR(cc.Vec2.ZERO))
```

---

### 19. 如何使代码块在一帧执行
* `scheduleOnce(CallBackFunction, 0);`

---

#### 20. Creator meta 文件的作用，编辑项目的时候 关于 meta 文件需要注意什么
* meta 文件是 Creator 编辑器在导入资源文件（asset）时创建的，用于记录引擎使用该资源时所需的数据
* 每个资源文件/文件夹都在成功导入到 assets 中之后，都会生成一个对应的 meta 文件
* 不同类型的资源，meta 文件的内容是不同的。但是每个 meta 都一定会有的两个属性
  * uuid：该资源的唯一标识符，meta 文件创建时随机生成
  * ver：meta 文件的版本号，由资源对应的 meta 类定义，主要用于判断资源在不同版本的 Creator 编辑器中是否需要重新导入
* 其他的属性都是由资源类型对应的 meta 类定义的
* 请不要随意的直接在系统的文件夹中删除 meta 文件，删除资源务必在编辑器中操作，主要原因是：
  * meta 文件中的 uuid 是创建时随机生成的，而且 uuid 是资源的唯一标识符
  * 资源文件之间的引用都是依赖于 uuid 的，例如场景中使用了一张图片资源，那么在这个场景的 fire 文件中，会记录这个图片资源的 uuid
  * 一旦删除了 meta 文件，那么这个资源的 uuid 就发生了变化，之前使用这个资源的地方将会遇到无法找到资源的问题。

---

### 21. Creator中销毁节点要使用什么方法， removeFromParent 和 destroy 有什么区别，调用 destroy 时会立即销毁节点回收组件吗
* 使用destroy
* removeFromParent方法执行的操作是从从父节点上移除这个节点，执行完后会立即从父节点上移除，最终会销毁节点，但并不会触发onDestroy回调，执行removeFromParent操作之后可以再为节点指定一个新的父节点
* destroy调用后不会立即从父节点上移除，而是在当前帧结束的时候移除并销毁节点，并会触发onDestroy回调，操作之后不能再为节点指定一个新的父节点。
* [参考资料1](https://forum.cocos.com/t/removefromparent-destroy/38396/10?u=1712655110)
* [参考资料2](https://forum.cocos.com/t/removefromparent-destroy/38396/14?u=1712655110)

---

### 22. Creator中 get set 方法有什么作用
* 使用 . 操作符可以调用 get 方法
* 使用 = 操作符可以调用 set 方法
* 一般用在关联数据与UI或者关联数据与数据这方面

---

### 23. 捕鱼路径规划如何实现

---


### 24. 捕鱼场景同步如何实现

---

### 25. 一张1024x1024,32位的贴图，在内存里面占多少字节？
* 1024 * 1024 * 32 / 8 = 4M字节

---

### 26. 使用对象池时，想要在节点回收或者复用时执行某些操作，有什么方法？
* 在 put 和 get 方法调用的地方执行
* 创建对象池时指定组件，在组件的 unuse 和 reuse 回调中执行


----------------------------------------

## JS和TS问题

### 1. js 对象引用传递会造成哪些问题，如何避免
* 引用传递可能会导致对象在不知名的地方被改变
* 避免问题需要合理使用只读属性或使用深拷贝传递对象

---

### 2. 删除数组中一些值或者遍历删除一些子节点的时候应该采用什么方法
* 后序遍历删除，或记录对应节点引用之后删除

---

### 3. 函数中this指向什么
* this指代函数的运行环境
* 使用 . 操作符调用函数时，this指操作符的左值
* call, apply, bind 都可以改变函数执行时的运行环境，即 this 的指向

---

### 4. 在脚本开发中如何实现同步
* 使用 promise
```
public async loadRes(url:string) {
    return new Promise<typeof cc.Asset>((resolve, reject) => {
        cc.loader.loadRes(url, (err, asset) => {
            if (err) {
                cc.error(err.message || err)
                reject(err.message || err)
            } else {
                resolve(asset)
            }
        })
    })
}
```

---

### 5. ts与js有什么区别，为什么用ts不用js
* TS 是 JS 的一个超集
* TS 的优势在于类型检查和代码提示
* TS 在最终会被编译成 js

---

### 6. 如何实现对象深拷贝
* `JSON.stringfy()`
* `JSON.parse()`
* `Object.assgin()`
* `Array.slice()`

---

### 7. ES6为什么要引入let关键字
* 因为要解决 var 声明对象产生的问题
* var 是函数级作用域，而let是块作用域
* var 存在变量提升，即变量可以在声明之前使用，值为undefined，而let声明的变量如果在声明之前使用会抛出一个错误
* 另外let不允许重复声明变量
* ES6 规定暂时性死区和 let、const 语句不出现变量提升，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为
