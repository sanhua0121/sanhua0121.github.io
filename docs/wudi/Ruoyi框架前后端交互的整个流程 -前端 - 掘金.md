   

[![](https://p3-passport.byteacctimg.com/img/mosaic-legacy/3795/3047680722~300x300.image)](https://juejin.cn/user/3897116787484488)

2022年05月25日 22:30 ·  阅读 82

持续创作，加速成长！这是我参与「掘金日新计划 · 6 月更文挑战」的第1天，[点击查看活动详情](https://juejin.cn/post/7099702781094674468 "https://juejin.cn/post/7099702781094674468")



**目录**

[第一部分 前端](https://juejin.cn/post/7101681167740452894#%E7%AC%AC%E4%B8%80%E9%83%A8%E5%88%86%20%E5%89%8D%E7%AB%AF "#%E7%AC%AC%E4%B8%80%E9%83%A8%E5%88%86%20%E5%89%8D%E7%AB%AF")

[1 当前网页对应的网址是localhost/system/role对应哪个页面](https://juejin.cn/post/7101681167740452894#1%20%E5%BD%93%E5%89%8D%E7%BD%91%E9%A1%B5%E5%AF%B9%E5%BA%94%E7%9A%84%E7%BD%91%E5%9D%80%E6%98%AFlocalhost%2Fsystem%2Frole%E5%AF%B9%E5%BA%94%E5%93%AA%E4%B8%AA%E9%A1%B5%E9%9D%A2 "#1%20%E5%BD%93%E5%89%8D%E7%BD%91%E9%A1%B5%E5%AF%B9%E5%BA%94%E7%9A%84%E7%BD%91%E5%9D%80%E6%98%AFlocalhost%2Fsystem%2Frole%E5%AF%B9%E5%BA%94%E5%93%AA%E4%B8%AA%E9%A1%B5%E9%9D%A2")

[2 点击某个页面，上面的数据是从数据库来的，前端如何发送请求给后端呢？](https://juejin.cn/post/7101681167740452894#2%20%E7%82%B9%E5%87%BB%E6%9F%90%E4%B8%AA%E9%A1%B5%E9%9D%A2%EF%BC%8C%E4%B8%8A%E9%9D%A2%E7%9A%84%E6%95%B0%E6%8D%AE%E6%98%AF%E4%BB%8E%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9D%A5%E7%9A%84%EF%BC%8C%E5%89%8D%E7%AB%AF%E5%A6%82%E4%BD%95%E5%8F%91%E9%80%81%E8%AF%B7%E6%B1%82%E7%BB%99%E5%90%8E%E7%AB%AF%E5%91%A2%EF%BC%9F "#2%20%E7%82%B9%E5%87%BB%E6%9F%90%E4%B8%AA%E9%A1%B5%E9%9D%A2%EF%BC%8C%E4%B8%8A%E9%9D%A2%E7%9A%84%E6%95%B0%E6%8D%AE%E6%98%AF%E4%BB%8E%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9D%A5%E7%9A%84%EF%BC%8C%E5%89%8D%E7%AB%AF%E5%A6%82%E4%BD%95%E5%8F%91%E9%80%81%E8%AF%B7%E6%B1%82%E7%BB%99%E5%90%8E%E7%AB%AF%E5%91%A2%EF%BC%9F")

## 第一部分 前端

### **1 当前网页对应的网址是localhost/system/role对应哪个页面**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7a4229c12964b62b8689777993666d3~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

前端路由是动态的，url来自于数据库；

前端文件夹名称以及文件名称、位置与数据库里一一对应

1.1、路由动态赋值

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bd606967d44c47d7ba614c4ed6afe35e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f80e790417564f88a2ec280aee76bfbf~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

如果地址是/system/role 就找一个system文件夹下面有role文件夹的文件

### **2 点击某个页面，上面的数据是从数据库来的，前端如何发送请求给后端呢？**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f34251260a942d88b9082fed7f37a86~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 刚加载页面时，发送请求给后端，用created或者mouted{}

getList()是一个封装的请求，listRole在api的js文件中

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/28601b61a10d45fa9cbef87f78eccca4~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 你会发现vue里引入js的变量，都是js里的方法名称

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b9f6ac3d19740629d607b33a3963411~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/512d087402e847c4821c67fc30426d0c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

```
export function listRole(query) {
  return request({
    url: '/system/role/list',
    method: 'get',
    params: query
  })}
复制代码
```

 _**后端的端口以及全称是什么呢？始终看不到真实地址？**_

**我们继续看request这个方法，在utils/request下**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b7072e426ea4ea6889fef0c1082ef53~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/19c0d57642424028bd97b35b40e1def5~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑 

 **普通的前后端交互，看demo,  axios实例**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/371c5a6170384cb09fe61aa183ba92ff~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

**这里注意，vue分开发环境与生产环境，这个**

```
process.env.VUE_APP_BASE_API
复制代码
```

 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a7bd1781459c45eabd528ff48d5b0cea~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 **这里/prod-api代表什么？**

**vue** **项目前端页面相互访问，是通过router就可以实现。**

**而数据交互的前后端存在跨域（跨域就是端口不一样或域名不一样），前后端交互频繁，若服务器地址后期变更，不方便维护。**

**所以我们需要一个精简的路径来映射真实后端请求路径。**

**在开发环境下，使用vue的proxy就可以实现前后端交互：**

**Vue-cli3做例子：在vue.config.js里面有信息**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/da4af56e9d0b4c8ca5939ce7170c0772~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

```
 devServer: {
    host: '0.0.0.0',
    port: port,
    open: true,
    proxy: {
      [process.env.VUE_APP_BASE_API]: {
         target: `http://localhost:8090`,
       // target: `http://192.168.1.64:8080`,
        changeOrigin: true,
        pathRewrite: {
          ['^' + process.env.VUE_APP_BASE_API]: ''
        }}},
    disableHostCheck: true},
复制代码
```

**Proxy:{}** **是代理服务器**

**pathRewrite** **意思是重写路径**

process.env.VUE\_APP\_BASE\_API

**总结上述：上面提到axios请求url都有一个公共的头部baseurl，指向process.env.Vue\_APP\_BASE\_API，其值为/prod-api。在代理服务器中，把真实的后端请求地址与/prod-api映射起来了。**

**例子**

**前端地址：ip:80**

**后端地址：ip:8080/项目名**

**存在跨域**

**前端配置映射   /prod-api   -----------** **à** **ip:8080/** **项目名**

  changeOrigin: true,// \*\*\*\***允许跨域的必备代码**

**生产环境下，以上**devServer: {proxy….}不起作用的，可注释掉（不注释也行），**在Nginx里配置就可以了**

```
user  root;
worker_processes  1;
#pid        logs/nginx.pid;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  192.168.209.236;
        location / {
            root   /usr/share/nginx;
            index  index.html index.htm;
            try_files $uri $uri/ /index.html;
}
           location ^~ /prod-api/ {
           proxy_pass http://192.168.209.236:8080/ruoyi-admin/;} }}
复制代码
```

**总结：只要请求存在跨域问题，就需要一个代理服务器把真实的后期请求地址与一个自定义的简短路径映射起来**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c5b671968520419c99548c49acb87e21~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)编辑

 **这个request封装axios方法，axios请求，都有一个公共的头部/prod-api，经过代理服务器映射，会转发到真实的后端服务器上**



![](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/00ba359ecd0075e59ffbc3d810af551d.svg) 赞

![](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/3d482c7a948bac826e155953b2a28a9e.svg) 收藏