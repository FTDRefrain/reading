## 简单介绍

1. DNSpod，进行域名配置
2. ubuntu使用的是apt-get获取包内容
3. node一个核心价值就是脱离浏览器进行开发
4. NodeJS Stream是前端工程化的基础
5. 三层结构：前端+后端+服务端
6. TC39看一些js的提案
7. 不要去循环引用，即A用B，B又用A
8. nodemon进行热更新；配合IDE里面的debug可以实现热更新的同时打断点调试

---

## Node线程相关

1. 进程是资源分配的最小单位。作为线程的容器。多进程是进程的fork，拥有独立的空间地址和数据栈，要通过IPC进行数据访问。
2. node app.js即开启一个线程

---

## KOA

1. 中间件传递过程中，最重要的就是ctx和next两个内容。ctx是上下文，next则是下一个中间件的闭包实现；自然可以返回内容，返回的就是一个promise对象

2. Async / await很关键，本身源于C#。因为本身next是一个promise，为了确保洋葱模型，要使用await保证内部执行完毕后再进入下一层，没有则失败

3. POST是新增；PUT是更新

4. await一个作用是直接对表达式进行求值

5. Process.pwd获得目录的绝对路径

6. 洋葱模型和ctx上下文传递的优点

   1. 内部结果上传方便，因为可以`ctx.result = result`然后再想用的中间件里面去ctx上面找
   2. ctx.body挂载内容作为返回

7. Koa-router进行路由的控制

   1. 实例化一个router；编写router，方法，路径及中间件函数；app.use挂载router.routes和router.allowMethods
   2. 路由的版本兼容性：api目录下构建不同的目录，支持多版本的路由
   3. 批量路由注册：require-directory直接去拿路径下面的所有内容，三个参数。module, path, {visit: test}，第三个是过滤函数

8. koa-bodyparser进行body的解析，本身是一个中间件

9. 异常处理

   1. 主要函数调用要try catch，另外就是错误的时候是throw error；本身对于异步处理很麻烦，使用promise使用很方便；大概代码如下，这样上层就能使用await去标识然后catch住

      ```jsx
      return new Promise((rs, rj)=>{
        rj('error situation')
      })
      ```

   2. 已知错误和未知错误。已知错误可以通过new Error声明，然后更改status和code去throw进行情景的合并和顶层的catch。例如e.status去设置状态码

   3. 推荐使用全局异常处理：采用的是中间件的思路

      ```jsx
      const catchErro = async (ctx, next) => {
        try{
          await next()
        } catch(e){
          ctx.body = e
        }
      }
      ```

   4. AOP面向切面编程，即不考虑里面的业务逻辑而是单独看整体结果

   5. 可以自己封装类继承Error，这样就可以引用和OO封装实现功能

   6. 里面使用global挂载全局的错误，使用的时候就是new一下得到实例

   7. 已知异常可以使用instanceof去进行处理，未知异常就是直接设置body去做；

   8. 校验使用validator，操作常规函数使用loadash

---

## 数据库

1. navicat去进行数据库可视化，sequenlize进行数据库的链接
2. 小程序会给用户生成一个openid，一对一的唯一id；对于所有的微信产品则是unionID