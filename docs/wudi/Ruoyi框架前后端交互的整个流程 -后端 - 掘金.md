   

[![](https://p3-passport.byteacctimg.com/img/mosaic-legacy/3795/3047680722~300x300.image)](https://juejin.cn/user/3897116787484488)

2022年05月26日 21:31 ·  阅读 75

 持续创作，加速成长！这是我参与「掘金日新计划 · 6 月更文挑战」的第2天，[点击查看活动详情](https://juejin.cn/post/7099702781094674468 "https://juejin.cn/post/7099702781094674468")  后端是如何接受前端的请求

ruoyi框架的后端整体结构

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4030f51ced8d469fae7a44756e0d6b69~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

若依框架看似有多个独立的项目，但只有一个入口程序（ruoyi-admin里面RuoYoApplication），入门程序一旦启动，其他项目都会启动起来。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/76222b8e8f60462ea4e1d8e6fce43c13~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

Ruoyi-common是注解、过滤器，异常及工具类的封装。

Ruoyi-framework是事务管理；

Ruoyi-generator是自动生成bean等映射文件及vue前端；

Ruoyi-quartz是定时任务的配置

Ruoyi-system主要是bean类，接口类与实现类等;

Ruoyi-admin是业务层，直接与前端交互

业务层在ruouyi-admin里，从包名就可以看出具体的请求方法

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/25678bd96e31457a94e1ba1fdca91b93~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

但是以下这个注解是什么意思呢？

@PreAuthorize

答案：权限认证，用户是否有操作的权限，有则执行，无则报错

@GetMapping("/list")

答：是路径，一个方法的全路径=类上路径+方法上路径；

若类上无路径，该方法的全路径就是方法上的路径；

前后端请求的案例：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0b958a8613a24f3886cdec93856ee5fa~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5231a828a116405586a9d4f43121e9fc~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4014b23ff40c44a7ad88b7f7ea75098e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab5502b2baf14c8386e54e4c498d2d82~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ccc4723508244032aa1790fc75e4a62c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

请求数据库到此结束。

疑惑：

各大模块之间都有独立的pom.xml文件，各个独立的模块之间如何通信的？？？

答：1、引入类  2、@Autowired自动装配

为什么前端发送请求给这个项目，项目就能够精准的找到某个类的方法里呢？

答案：拦截器。所有请求，经过拦截器，都能精准转发。

它是如何实现数据库与bean类映射的？

Mapper文件与数据库关联。

它为什么不需要写映射文件就可以呢？什么起作用？

答案：有映射文件，只不过若依框架只需要执行sql语句，在前端就能自动生成bean service mapper以及xml文件，直接下载既可。

这个方法中的参数(SysRole role)是不是不需要的，因为前端没有传递参数过来？

答案：在这个查询数据库中所有数据是不需要的，但是这个方法还对应前端条件查询，所以参数必不可少。

List数据为什么不需要封装成jsonobject或者jsonarray就可以给前端，并且前端返回的还是…

答案：封装了，有专门的类 ，也有封装成Object自定义类 

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/309dabb9b77b49e0a4bcb7972dc684b8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/38a464dcbac54fac80044724523a532f~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑 

 后端如何把数据返回给前端的

答案：直接return就可以了，类必须是@RestController而不能是@Controller，前者返回数据，后者是调转页面。

前端是如何展示数据的？

答案：获取后端数据，然后通过v-for渲染

数据权限在前后端是如何控制的？

答案：不同的用户拥有不同的权限，登录成功时，查询用户拥有的权限并与token存储在redis里，前后端根据用户自带的权限展示出可以展示的。

证明：先登录一个普通的用户，看看页面的网络信息。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2905d8e3e5154549aa8d9d3e841ec67d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f2ed5f44c56f42b783bbbd65fb32daff~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

在点击登录的时候，首先验证用户名与密码是否正确。然后返回一个口令 

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/36ead02fc29f4628afb0a3e65816251c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f80f87de6974196b55c9f70382bd14d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d881025fed346eab4e5a88d34e239c3~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑 数据给前端，前端通过一些校验来显示或者隐藏一些东西

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d2699791b7d543fba8a411a6cb05b801~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

1.  后端采用权限限制访问，请问前端传什么值过来了，后端才放行了，为什么我们没看到呢？

答案：前后端访问是token令牌，每个用户都不同的token，token与该用户权限以键值对的方式存储。前后端根据这个用户权限展示或隐藏

那么为什么后端会设置权限呢？

答案：防止postman以及爬虫人员解析出后端地址，没有后端权限，就能直接访问后端

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/df6628e074de48acbd8f9cbe807430bf~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

我们拿postman做实验：

管理员的特殊权限，普通用户看不到，但是当你知道后端地址，是不是就可以自己用postman查看返回的数据。

后端有权限设置，你没有token就不能访问后端，自然看不到数据

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/615de872cbb44dfda5f3fd4f3d967bc8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

是不是只要有token就可以拿到数据？

是的！！！

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1a83ef09e68b497586fd566caa333db2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b7990f985b7b402fb076fc8d2ffb82d9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑



![](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/00ba359ecd0075e59ffbc3d810af551d.svg) 赞

![](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/3d482c7a948bac826e155953b2a28a9e.svg) 收藏