# CocosCreator 常见问题

## CocosCreator开发相关问题
### 业务相关
#### 1. CocosCreator和cocos2d-x的区别
> 实现不同，Creator引擎是采用cocos2d-x-lite + jsb 和 js engine 分别实现原生和h5平台，cocos2d-x-lite 是基于cocos2d-x 3.9 的简化版，2d-x基本属于代码驱动，Creator优化了相关工具链，提高了开发效率
cocos creator是一套包含编辑器在内的开发框架，其内部引擎使用了cocos2d-x js的精简修改版本

---

#### 2. CocosCreator如何实现多平台发布的
> 原生平台使用 cocos2d-x-lite + jsb ，h5平台使用js engine运行于浏览器环境

---

#### 3. Creator中需要动态载入的资源，放在工程的哪个子目录中 
> resources

---

#### 4. 资源动态加载和非动态加载的区别
> 使用 cc.loader 加载的属于动态加载，直接在场景上拖上去的属于非动态加载，非动态加载资源参与场景销毁时的资源自动释放，动态加载不会，需要手动释放资源

---

#### 5. 释放资源时要注意哪些问题
> 需要注意被释放的资源是否被引用，否则会渲染出错

---

#### 6. 生命周期回调有哪些，触发的时机

*  onLoad   组件首次激活时触发
*  start    组件第一次执行update之前触发
*  update     每一帧渲染前调用
*  lateUpdate 所有组件update调用后调用
*  onDestroy  组件或所在节点调用了destroy()时调用，并在当前帧结束时统一回收组件
*  onEnable  组件的enabled属性从false变为true时
*  onDisable  组件的enabled属性从true变为false时

---

#### 7. Creator对齐UI使用什么组件
> widget

---

#### 8. Creator屏幕适配有哪些心得

---

#### 9. 游戏性能指标有哪些
> 内存、CPU、DC、FPS

---

#### 10. draw call 如何优化，如何合并
> 合并 drawcall 的规则是这样的：
> - render command 相邻，大致可以理解为节点树中顺序相邻
> - texture 一致
> - blend function 一致
> - shader program 一致
> - label会打断drawcall合并，所以尽量使可以合并的节点相邻，避免label在中间打断

---

#### 11. spine动画能否合并draw call
> 不能

---

#### 12. 被Mask组件裁切不在显示区域内的节点会占用draw call吗
> 会

---

#### 13. 如何优化ScrollView
> 隐藏显示区域外的节点，active 为 false 的节点不占用 draw call，复用列表项，使用对象池管理列表项

---

#### 14. 如何进行资源和内存优化
> 图片压缩、使用图集、使用动态图集、权衡序列帧和骨骼动画的使用、使用合理的策略进行资源的加载和释放

---

#### 15. 一般而言造成FPS低的直接原因是什么，如何优化FPS
> 原因是主循环中游戏逻辑占用太多时间，比如大量频繁的创建和销毁节点、大量频繁的加载和释放资源、过多的运算等，优化方法是压缩运算量、采用分帧操作避免卡帧，使用对象池

---

#### 16. 使用全局单例要注意哪些问题
> 需要格外注意释放

---

#### 17. 逻辑开发如何解耦
> 合理使用事件系统有助于逻辑解耦

---

#### 18. a节点和b节点非父子关系，如何获取a相对b节点的坐标

```
b.convertToNodeSpaceAR(a.convertToWorldSpaceAR(cc.Vec2.ZERO))
```

---

#### 19. 如何使代码块在一帧执行
> setTimeOut 或用计时器

---

#### 20. Creator meta 文件的作用，编辑项目的时候 关于 meta 文件需要注意什么

---

#### 21. Creator中销毁节点要使用什么方法， removeFromParent 和 destroy 有什么区别，调用 destroy 时会立即销毁节点回收组件吗
> 使用destroy。removeFromParent方法执行的操作是从从父节点上移除这个节点，执行完后会立即从父节点上移除，最终会销毁节点，但并不会触发onDestroy回调，执行removeFromParent操作之后可以再为节点指定一个新的父节点。destroy调用后不会立即从父节点上移除，而是在当前帧结束的时候移除并销毁节点，并会触发onDestroy回调，操作之后不能再为节点指定一个新的父节点。[参考资料1](https://forum.cocos.com/t/removefromparent-destroy/38396/10?u=1712655110)，[参考资料2](https://forum.cocos.com/t/removefromparent-destroy/38396/14?u=1712655110)

---

#### 22. Creator中 get set 方法有什么作用
> 使用 . 操作符可以调用 get 方法，使用 = 操作符可以调用 set 方法，一般用在关联数据与UI或者关联数据与数据这方面

---

#### 23. 捕鱼路径规划如何实现

---


#### 24. 捕鱼场景同步如何实现

---

#### 25. 一张1024x1024,32位的贴图，在内存里面占多少字节？
> 1024 * 1024 * 32 / 8 = 4M字节

---

#### 26. 使用对象池时，想要在节点回收或者复用时执行某些操作，有什么方法？
> 在put个get方法调用的地方执行，或者创建对象池时指定组件，在组件的unuse和reuse回调中执行


----------------------------------------

### JS和TS问题

#### 1. js 对象引用传递会造成哪些问题，如何避免
> 引用传递可能会导致对象在不知名的地方被改变，避免问题需要合理使用只读属性或使用深拷贝传递对象

---

#### 2. 删除数组中一些值或者遍历删除一些子节点的时候应该采用什么方法
> 后序遍历删除，或记录对应节点引用之后删除

---

#### 3. 函数中this指向什么
> this指代函数的运行环境，使用 . 操作符调用函数是，this指操作符的左值，call, apply, bind都可以改变函数执行时的运行环境，即this的指向

---

#### 4. 在脚本开发中如何实现同步
> 使用 promise
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

#### 5. ts与js有什么区别，为什么用ts不用js

> TS是JS的一个超集，优势在于类型检查和代码提示，ts在最终会被编译成js

---

#### 6. 如何实现对象深拷贝
1、JSON.stringfy()和JSON.parse()
2、Object.assgin()，Array.slice()

---

#### 7. ES6为什么要引入let关键字

> 因为要解决var声明对象产生的问题。
> var是函数级作用域，而let是块作用域。
> var存在变量提升，即变量可以在声明之前使用，值为undefined，而let声明的变量如果在声明之前使用会抛出一个错误。
> 另外let不允许重复声明变量。
> ES6 规定暂时性死区和let、const语句不出现变量提升，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。这样的错误在 ES5 是很常见的，现在有了这种规定，避免此类错误就很容易了。
