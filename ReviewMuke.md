1. ES6语法相关

   1. ES模块化如何使用，开发环境如何打包

      1. rollup：和webpack不一样的东西，也是用来打包的东西，本身vue和react都是使用这个打包的；

         1. 只能进行模块化，但是打包效果特别好；

         2. 配置代码如下

            ```javascript
            export default {
              entry:'src/index.js',
              // 表示兼容所有的模块方式，amd, cmd等
              format:'umd',
              plugins:[
                resolves(),
                babel({
                  exclude:'node_modules/**'
                })
              ]
              dist:'bundle/bundle.js',
            }
            ```

            

      2. Babel：JS编译器，通过.babelrc进行配置

   2. class和普通构造函数的区别

      1. 构造函数

         ```javascript
         // 构造函数
         function Cons(x, y){
         	this.x = x;
           this.y = y
         }
         // 原型添加方法
         Cons.prototype.add = function() {
           return this.x + this.y
         }
         // 实例化
         let a = new Cons()
         // 特性，即new生成的可以通过原型链找到原来的东西；
         a._proto_ === Cons.prototype //true
         ```

      2. class本身是语法糖，一个特性就是`Cons = Cons.prototype.constructor`，这样就实现了class；

      3. 继承

         1. ES6之前的方式

            ```javascript
            function C1(){}
            function C2(){}
            C2.prototype = C1
            let c = new C2()
            c._proto_ === C1 //true
            ```

         2. 之后就直接是extends

   3. Promise相关内容

      ```javascript
      const pro = new Promise((resolve, reject)=>{})
      ```

      每次的then都是传入两个回调，成功和失败；

   4. 新转化的方式，[a, b] = [b, a]；

2. 原型

   1. 原型的实际应用

      1. jquery中的内容

         1. 如下代码就是例子，可以看到**不同实例拥有相同的方法**，**即通过原型实现的**

            ```javascript
            var $p = $('p')
            var $div = $('div')
            $p.css('color', 'red')
            $div.css('width','80%')
            ```

         2. 实现方式如下

            ```javascript
            (function (window){
              var Jquery = function (selector){
              	return new Jquery.fn.init(selector)
              }
              Jquery.fn = Jquery.prototype = {
                construtor: Jquery,
                // 其他函数
              }
              // 预处理，拿到选择的所有内容
              var init = Jquery.fn.init = function(selector){}
              // 这样init的时候就能获取到其他所有的方法且指向相同；
              init.prototype = Jquery.fn
              // 简化变量
              window.$ = Jquery
            })(window)
            ```

         3. 赋值的时候是直接改变指针，上面的连等实际上是先找左边两个的this，没有就用null赋值，然后把这些this都变成{n : 2}；所以执行结果如下

            ```javascript
            let a = {n:1}
            let b = a
            a.x = a = {n:2} //创建x，然后x和a都指向{n :2}
            a // {n:2}
            b // {n:1, x:{n:2}}
            ```

      2. 原型的扩展性

         1. 上面的Jquery.fn是用来添加插件的，因为其他开发可以使用$.fn.addFunc来添加自己的方法，好处是只暴露了唯一变量​

3. 异步

   1. 单线程原因：避免DOM渲染的冲突且JS执行时DOM渲染也停止

   2. 解决方案：异步，无奈的选择

   3. webworker可以实现多线程但是不能操作DOM

   4. Event-loop

      1. 轮训查找异步队列，当同步内容都执行完毕开始执行异步队列内容
      2. setTimeout是将内容延迟特定的时间后放到了队列里面且最小为4ms，所以优先级最低
      3. ajax则是将自己的callback内容放到了异步队列里面；

   5. Jquery Deferred

      1. 即将之前的ajax写法变成.then或者.done和.fail的形式

      2. 开放封闭原则：即对扩展开发对修改封闭，像前面的$.fn，很容易扩展但不能去修改原来的方案；

      3. 实现方式如下

         ```javascript
         function newAjax(){
           let dtd = $.Deferred()
           const task = (dtd) =>{
             const cb = ()=>{
               dtd.resolve()
             }
             setTimeout(cb, 2000)
             // 这里返回的是promise而不是完整的dtd
             // 因为可以手动reject和resolve，但应该只能then, done和fail
             // 使用的就是$.when(newAjax).then()
             return dtd.promise()
           }
           return task(dtd)
         }
         ```

   6. 现在的Promise

      1. 本身是window变量里面的

      2. 可以最后面放.catch用来捕获所有的错误

      3. 多个promise的链式操作，代码如下，即第一个then返回了p2，这时第二个then的结果就是p2的结果

         ```javascript
         let p1 = new Promise();
         let p2 = new Promise()
         p1.then(()=>(
         	p2
         )).then(()=>{
           console.log(p2.result)
         }).catch((e)=>{
           console.log(e)
         })
         ```

      4. Promise.all和Promise.race，使用代码如下

         ```javascript
         // 首先这里面的Promise是全局变量，all表示所有成功才执行
         Promise.all([result1, result2]).then((datas)=>{
           console.log(datas[0])
         })
         // 表示有一个成功即执行
         Promise.race([result1, result2]).then((datas)=>{
           console.log(datas[0])
         })
         ```

      5. Promise标准：

         1. 状态变化：pending - fulfilled或者rejected，变化过程不可逆；

      6. Async/await

         1. Babel-polyfill编译后才能兼容使用；

4. 虚拟DOM

   1. 结构如下，即tags表示类型，attrs表示所有的属性，children表示子节点（可以是内容也可以是标签）；
   
      ```javascript
      {
        tag:'ul',
        attrs:{
          id:'list'
        },
        children: [
          tag:'li',
          attrs:{
            id:'listChild'
          },
          children: ['items']
        ]
      }
      ```
   
      















































































