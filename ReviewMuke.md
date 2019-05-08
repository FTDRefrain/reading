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

      3. `null >= 0`是true

      4. []和null的type是object；

      5. 关键字不能使用，同样也不能作为对象的方法被赋值和使用；

      6. -不能使用，但是可以使用_，或者就是使用""将名字包裹起来；

      7. 使用||和&&进行不存在值的避免；

      8. for里面的in取的key，of取的是值；in无法控制出来内容的顺序；

      9. 每个函数接受两个参数：this, argument；四种调用方式，不同的调用方式决定了this的差异

         1. 方法调用，即在对象里面声明，这时候this指向声明的对象
         2. 函数调用，并非对象的一个属性时，this指向全局，可以用变量接住this，表示正确的指向；
         3. 构造器调用，即new的方式，指向新构建的对象；
         4. apply方式，即第一个参数指定this指向方式；
         5. argument进行隐世传递，本身是类数组，只用length没有其他的数组方法；

      10. throw扔出的对象由外面的catch接住；

      11. 尾递归，当函数返回自身递归调用的结果，过程可以通过循环去优化，但是js没有；

      12. 模块模式，即通过闭包的方式解决一些全局变量的隐患，返回的是希望暴露的内容；因为闭包的东西可以访问但是不能修改；

      13. 级联，即不断的调用方法，本身编写的时候方法返回的是this就可以了；

      14. 柯里化，将复数参数转化为单参数函数进行调用，即将之前的参数通过闭包和argument联合都整合到一起；

      15. new实现方法如下

          ```javascript
          Fuction.prototype.extends = function(){
            var that = Object.create(this.prototype)
            var others = this.apply(that, arguments)
            //这里要返回一个对象
            return typeof others === 'object' ? others : that
          }
          ```

      16. 继承的话如下实现，即原型指向继承对象，自己写构造函数；

          ```javascript
          var Cons = function(x, y){
          	this.x = x;
            this.y = y
          }
          Cons.prototype = new Parents()
          var children = new Cons
          ```

      17. 私有变量实现：即另外加一个that，将需要暴露的东西加到这个that里面，然后return出去而不是原来的that；

      18. splice进行数组内容的删除，两个参数，一个是序号，一个是个数；

      19. 数组本身也是对象，上面可以挂在方法且不会增加长度；

      20. 数组的fill函数进行初始值的设定；

      21. sort方法不能正确的排序，要自己设定；

      22. NaN：将非数值的字符串转换成数字的时候出现，本身不会被js识别，即NaN ===  NaN是false；

      23. 数组的判断使用Object.prototype.toString.apply(testValue)去检测；

      24. Symbol用来生成全局唯一值，一些频繁使用字符串进行判断的地方应该使用变量，但是变量的值又无所谓，反正只要变量的key，这时候就需要symbol了

      25. 循环的for里面的变量i和函数体内部的变量不在同一个作用域里面，即可以设置相同的变量

      26. 块级作用域里面的变量在let和const声明之前是不能调用的，即绝对的没有变量提升，形成了暂时性死区，且typeof也会出错；变量提升的问题在于使用变量的时候会先从内部开始找，找到了就先用undefined占位置；

      27. Object.freezeq确定对象不可变但是也是最外层，即里面有对象也要递归；

      28. const保证的是指针不变，所以对象可以添加和修改属性却不能赋值为另一个对象；

      29. 解构赋值是通过迭代器实现的，即当后面的结构具有迭代结构，可以实现next()方法，就是通过不断调用next进行相似结构赋值；

          - 接受默认值，但是对面要是严格的undefined，即null也是会被赋值的；
          - 默认值的实现，本身是惰性的，即当后面的内容不是undefined的时候就不会被执行，不管是函数还是未定义的变量；
          - 对象自然也可以进行赋值

      30. 

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
         2. _proto_：对象特有，指向上层的prototype，这样对象就可以继承了prototype中的属性和方法；
         3. Prototype，函数特有的，用于存储共享的属性和方法；
         4. Constructor，函数特有，定义在prototype里面；

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
   
      















































































