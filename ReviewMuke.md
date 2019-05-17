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

      30. 模板字符串中是可以使用函数的，即也可以进行嵌套；不是字符串会调用对象的toString方法；

      31. 正则四个方法：match, replace, search, split；

      32. 函数的length表示预传入参数的个数，意味着使用了默认值之后length会改变且尾参数没有给默认值的情况下，最后一个默认值之后的内容都不要；

      33. 函数传参的地方有自己的作用域，且默认值可以是函数；能利用默认值抛出参数缺少的异常；

      34. rest函数就是…args，一个有用的点就是合并参数，是展开…函数的逆运算，和展开不同的是展开后面可以补充其他内容，rest的合并后面不能再给值了，代码如下

          ```javascript
          const numbers = (...nums) => nums;
          numbers(1,2,3,4) // [1,2,3,4]
          ```

      35. 箭头函数的this使用的是自己外层的this，自己本身没有this；

      36. 尾调用优化：函数的调用要从另一个函数回来，这样就需要记录函数的内部变量，函数调用位置等，但是尾调用的时候，如果没有使用函数内部的变量，直接记录函数调用帧就行，调用完直接回到上层函数就行

      37. 尾递归改写优化，优势就是不会发生栈溢出，代码实例如下

          ```javascript
          // 递归调用的时候要存上所有的n；
          function factorial(n){
            if(n === 1) return 1;
            return n * factorial(n-1)
          }
          // 下面就是一个尾递归优化，因为本身没有依赖上次的变量
          function facChange(n, total){
            if(n === 1) return 1;
            return facChange(n-1, n*total)
          }
          ```

          再看下面的例子

          ```javascript
          function Fibonacci(n){
            if(n <= 1) return 1;
            return Fibonacci(n-1) + Fibonacci(n-2)
          }
          // 同样改写，将需要的变量传进去而不是写在外面；
          function Fibo2(n, acc1=1, acc2=1){
            if(n <= 1) return 1;
            return Fibo2(n-1, acc2, acc1+acc2)
          }
          ```

          递归优化只在严格模式下生效，正常模式下就要自己去做了；

      38. 有迭代器的就可以通过...展开

      39. Array.from将类数组，有迭代器的东西变成数组，第二个参数类似map，将里面的东西处理后返回，即将第一个参数变成数组然后使用map进行处理后再返回；

      40. 数组keys, values, entries依次返回键，值，键值对

      41. Object.is方法用来判断两个值是否相等，补充了===里面的+0和-0相等，NaN不等于自身的问题；

      42. Set进行去重；

          - 初始的时候里面传入字符串的话就会是将字符串拆开变成数组，所以可以用于字符串内的字符去重；
          - 对象用于是不等的
          - 不会发生类型转换，即类似于===的判断标准
          - 本身由add, delete, has, clear四个操作方法
          - Keys, values, entries, forEach四个遍历方法

      43. Map，解决是只能使用字符串当做键名的问题，本身不是字符串或者变量不是字符串的会变；

          - 可以把对象当做键，准确说就是值-值的对应关系
          - set设置，get获得；
          - 本身记录的是指针位置，所以{}和{}就是两个值了

      44. Promise对象

          1. 两个特点：

             - 不受外界干扰：即只有里面的异步操作可以进行状态的更改，也是promise的由来
             - 一旦状态改变，任何时候都能获取；不像事件，错过了就监听不到了；

          2. 使用方式如下：本身接受两个cb，一个进then另一个进catch，且catch内容冒泡可以接受所有promise的reject；

             ```javascript
             const getJson = (url) => {
               return new Promise((resolve, reject)=>{
                 const handler = () => {
                   if(this.readyState !== 4) {
                     return
                   }
                   if(this.status === 200){
                     resolve(this.response)
                   } else {
                     reject(new Error(this.statusText))
                   }
                 }
                 const client = new XMLHttpRequest()
                 client.open('GET', url)
                 client.onreadystatechange = handler
                 client.responseType = 'json'
                 client.setRequestHeader = ('Accept', 'application/json')
                 client.send()
               })
             }
             ```

          3. resolve里面可以接受promise，这时old要看里面的状态，当里面的不是pending的时候才会执行；

          4. resolve和reject会改变状态但是不会终止代码的执行；但是resolve之后再throw Error的话就是改变状态所以不会执行，另外直接return也不会执行了，且在then里面return的也是Promise的话就能使用then

          5. 看下面的延迟throw Error，因为是异步的throw，所以在then的时候执行且在Promise外层，catch捕获不到；

             ```javascript
             const delayThrow = new Promise((r1, r2)=>{
               r1('ok')
               setTimeOut(()=>{
                 throw new Error('sorry')
               }, 0)
             })
             delayThrow.then(()=>{}) // ok sorry
             ```

          6. finally方法，不管状态如何都会执行；是下面的语法糖

             ```javascript
             Promise.prototype.finally = function(cb){
               let p = this.constructor
               return this.then(
                 value => {
                   // 这里要执行
                   p.resolve(cb()).then(()=>{value})
                 }, 
                 error => {
                   p.resolve(cb()).then(() => throw error)
                 })
             }
             ```

          7. 注意catch方法拿到的是异步的错误，try里面拿到的是同步的错误；简化方式如下，所有的错误都放到catch里面了；

             ```javascript
             Promise.try(()=>getData()).then().catch()
             ```

      45. Generator

          1. 异步，即分段执行的函数，之间可以插入其他的函数，实现了异步操作；即协程

          2. 利用回调实现，即分段函数就是当第一段有返回的时候执行cb；且错误必须作为参数传入第二段，因为执行完上下文就没了，所以错误只能在第二段执行

          3. thunk，实现传名调用；看代码，即使用的时候再计算，用来实现迭代器里面的同步执行

             ```javascript
             let x = 2
             const a = (x)=> x+2
             a(x+5)
             // thunk实现
             const thunk = () => x+5
             const a = (cb) => cb() + 2
             ```

          4. 迭代器里面的同步执行如下，即yield跳出迭代器，所以要另外有一个指令回到迭代器，手动执行如下

             ```javascript
             // g是迭代函数
             var g = gen();
             var r1 = g.next();
             // next返回的是value和done两个值；
             r1.value(function (err, data) {
               if (err) throw err;
               var r2 = g.next(data);
               r2.value(function (err, data) {
                 if (err) throw err;
                 g.next(data);
               });
             });
             ```

          5. Promise控制迭代器代码如下

             ```javascript
             var fs = require('fs');
             // 返回的是promise对象
             var readFile = function (fileName){
               return new Promise(function (resolve, reject){
                 fs.readFile(fileName, function(error, data){
                   if (error) return reject(error);
                   resolve(data);
                 });
               });
             };
             
             var gen = function* (){
               var f1 = yield readFile('/etc/fstab');
               var f2 = yield readFile('/etc/shells');
               console.log(f1.toString());
               console.log(f2.toString());
             };
             
             function run(gen){
               var g = gen();
             
               function next(data){
                 var result = g.next(data);
                 if (result.done) return result.value;
                 result.value.then(function(data){
                   // 一步一步的链式操作下去；
                   next(data);
                 });
               }
               next();
             }
             run(gen);
             ```

      46. async函数

          1. 本身是迭代器的语法糖，async替代了*，表示里面有异步操作；await替代了yield表示等待；

          2. 里面遇到await会返回promise对象，即可以通过then调用；没有await的话，本身的return就是一个promise对象；

          3. 内置了执行器，不用自己去next调用了；

          4. 依次实现动画，当动画有错误的时候停止且返回上一个值；实例代码如下

             ```javascript
             // promise实现
             function chainAni(elem, animes){
               // 存之前的值
               let ret = null
               // 链式的起点
               let p = Promise.resolve()
               for(let anime of animes){
                 p = p.then((val)=>{
                   ret = val
                   return anime(elem)
                 })
               }
               return p.catch().then(() => ret)
             }
             
             // Generator实现
             function chainAnime(elem, animes){
               // 返回是可以控制自动迭代的函数
               return spawn(function* (){
                 let ret = null
                 try{
                   for(let anime of animes){
                     ret = yield anime(elem)
                   }
                 } catch(e){
                   
                 }
                 return ret
               })
             }
             // async
             // 从形式也能看出语法糖的感觉
             async function chainAnime(elem, animes){
               let ret = null
               try{
                 for(let anime in animes){
                   ret = await anime(elem)
                 }
               } catch(e){}
               return ret
             }
             ```

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
   
   2. 大概的转换代码如下
   
      ```javascript
      // 伪代码
      function vDom(vnode){
        const { tag, attrs={}, children=[] } = vnode
        tag || return null
        // 创建元素
        const elem = document.createElement(tag)
        // 添加属性
        for(attrName in attrs){
          if(elem.hasOwnProperty(attrName)){
            elem.setAttribute(attr, attrs[attrName])
          }
        }
        // 添加子节点
        children.foreach((child)=>{
          // 递归构建子节点后连接上去
          elem.appendChild(vDom(child))
        })
        // 返回DOM元素
        return elem
      }
      ```
   
      
   
   3. snabbdom进行VDOM的实现：使用patch(container, newNode)和patch(oldNode, newNode)两种方式；
   
   4. DIFF算法，
   
5. MVVM和Vue

   1. ViewModel分离View和model：Dom Listener实现响应，data bindings实现数据驱动

   2. 响应式：

      1. 即数据实时更新，实现代码如下

         ```javascript
         let obj = {}
         let name = '123'
         // 这样对象的属性值才是动态的
         Object.defineProperty(obj, 'name', {
           get: function(){
             return name
           },
           set: function(newVal){
             name = newVal
           }
         })
         ```

      2. 模仿Vue实现如下

         ```javascript
         let vm = {}
         let data = {
           title:'123',
           content:'123'
         }
         for(let key in data){
           // 闭包实现，使用let，不是闭包也行了
           (function(key){
             Object.defineProperty(vm, key, {
               get: function(){
                 return data[key]
               },
               set:function(newVal){
                 data[key] = newVal
               }
             })
           })(key)
         }
         ```

   3. 模板解析

   4. render函数：

      1. 大致会进行如下的转换

         ```javascript
         <input v-model="title" />
         vm._c(
         	'input',
           {
             directives:[{
               name:'model',
               rawName:'v-model',
               // 这里因为是用with起手，所以变量就是vue的data里面的变量，即实现了数据绑定
               value:(title),
               expression:'title'
             }],
             domProps:{
               'value':(title)
             },
             // 挂载默认或者自己设置的method
             on:{
               'input':function(e){
                 title = e.target.value
               }
             }
           }
         ),
         ```

      2. 本身是实时渲染的，将html内容变成js代码

      3. render是将html变成vnode，然后在更新的时候调用update函数进行vnode之间的比较，之后再patch上去

         ```javascript
         // 放到双向绑定的set里面就可以了且初始自动执行
         function updateDom(){
           this._update(this.render())
         }
         const _update = (vnode) => {
           const preNode = this._vnode
           this._vnode = vnode
           if(!preVnode){
             // 全新是挂载
             this.$el = this.__patch__(vm.$el, vnode)
           } else {
             // 有内容是patch
             this.$el = this.__patch__(preNode, vnode)
           }
         }
         ```

      4. 整体的流程如下

         1. 解析模板生成render函数，使用with和return vNode使用的是_c(vDom)
         2. 定义数据的双向绑定；
         3. 首次渲染，显示页面且绑定依赖：这里面绑定依赖要监听get，因为有些属性并没有参与到render，即set不应该被触发，所以set的时候要监听是否get了，如果确实get过才应该添加set
         4. 属性变化执行更新，在set中执行update的过程是异步的；

6. React和组件化

   1. 特点：
      - 封装：视图，数据，变化逻辑
      - 复用：结构复用；
   2. JSX，可以使用在线或者命令行的transform-react-jsx进行内容的展现
      1. 本身是将html的东西放到了React.createElement里面
      2. 自定义组件：本身通过new构建一个实例，将props放进去，然后调用render方法生成html；
   3. setState
      1. 异步过程
      2. 在异步回调函数里面调用了patch函数，实现了render

7. Hybrid

   1. 本身并不是h5，例如一个页面，上的返回和下面的输入都是服务端的东西；中间的内容包括用户名字，内容等都是客户端的东西
   2. 优点
      1. 快速迭代更新：开发块且无需审核(脱离于native内容)
      2. 体验基本和native差不多
      3. 双端通用
   3. webView
      1. 浏览器的内核，承载html页面
      2. 本身可以作为app的一个组件
   4. file协议：本地文件，http协议则是网络加载；
   5. 实现方式：即把文件变成静态的页面(html + css + js)，然后在webview里面通过file协议进行内容的导入；流程如下
      - 构建版本号，将静态文件压缩成zip包，上传到服务端
      - 客户端启动时候进行版本号检查，不同则下载zip
      - 解压将文件覆盖
   6. 缺点：联调和测试麻烦，运维成本高；
   7. 使用场景：体验要求高和迭代频繁；h5适合的就是单次的
   8. JS-SDK进行JS和客户端的通信；
   9. 客户端获取内容，因为js要在dom加载完然后执行进行内容的获取，客户端可以直接获取内容，然后js获取客户端接口的内容；
   10. schema协议，就是客户端和前端通信；可以通过传参和回调函数的方式；简单的方式就是通过iframe.src='js-sdk?k1=1&callback=_cb1'这样的方式，因为要传回调，所以必须是全局挂载的，使用完毕后再删掉iframe；















































































