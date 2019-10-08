## 简介

1. 优势是：懒加载，tree-shaking, code-split等等
2. 阅读文档很关键
3. 记住，loader是从下到上的执行顺序，plugin则是从上到下的执行方式
4. 解决的问题：
   1. 希望做到代码复用，所以采用的是多文件OO编程的方式。问题是文件多，请求变慢且不知道函数来源，不利于维护
   2. 提出了使用模块化，即使用import的方式。所以依旧是单文件，且模块依赖很清晰；问题是client不知道
   3. webpack进行上面模块化的处理，即解决了问题；即本身就是一个模块打包工具，CMD，AMD，ES6 Module都可以正确识别

---

## webpack初探

1. npx `webpack index.js`进行js文件的翻译；npx表示在当前目录下运行，`-D`表示局部安装，`-g`是全局；使用`uninstall`去进行卸载；`npm info webpack`看包的信息；
2. 本身是基于node环境实现
3. `npm init`初始化一个配置文件
4. `webpack.config.js`进行配置文件的重写；或者通过`--config`去指定配置文件
5. `scripts`里面给的一一对应，用`npm run`去调用，且本身是先从同目录找，再去全局找
6. `webpack-cli`实现了通过命令行调用webpack的方式
7. 单入口的时候，`chunkName`就是main，也就是默认写法

---

## webpack核心内容

1. module字段

   1. 模块打包可能面对不同文件或者不同模块想要自定义，所以就要去配置module

   2. rules字段就是针对不同的文件采用不同的打包策略；

      1. test字段进行文件匹配，

      2. use指定配置：loader指定打包方案；options进行配置，大概如下，指定名字，里面的东西就是placeholder字段

         ```jsx
         options:{ 
           name:'[name]_[hash].[ext]',
           outputPath: 'images/',
           limit: 2048,
         }
         ```

   3. `file-loader`和`url-loader`功能相似，都是对静态资源文件进行打包

   4. css文件打包相关

      1. 使用复数的loader，即use之后使用数组就行，有配置则是对象；`style-loader`是将样式注入html里面，`css-loader`则是打包所有的css文件；

      2. scss要先安装`node-sass`然后在webpack上面挂载`sass-loader`

      3. `-webkit-`这种CSS的新属性，要做好前缀，安装`postcss-loader`去进行css的预处理

         1. 本身可以设置`postcss.config.js`，代码如下

            ```jsx
            module.exports = {plugins:[require('autoprefixer')]}
            ```

      4. css中使用import的情况，代码如下；importLoader的意思，是当里面出现import情况，多走之前的loader数量。下面的意思就是css-loader走的时候，出现import则再走sass和postcss

         ```jsx
         use:[
           {
             loader:'css-loader',
             options:{
               importLoader:2，
               module:true
             }
           },
           'sass-loader',
           'postcss-loader'
         ]
         ```

      5. 模块化，如上面的方式，即开启了module就是模块化

      6. iconfont直接使用file-loader就可以

2. plugin字段

   1. 即打包完成或者之前运行阶段进行一些额外的修饰或者操作
   2. Html-webpack-plugin，在打包结束后，根据模板构建html然后将js注入
   3. Clean-webpack-plugin，用来清除上一次打包出来的结果；明显就是打包前执行的

3. Entry和output

   1. entry里面的kv，对应的就是打包结果名字和入口文件的名字
   2. output里面的filename可以使用`[name].js`去进行名字修饰，名字就是之前entry里面的key
   3. output通过`publicPath`指定打包后js文件的路径前缀，这样就能配合CDN使用

4. Source-map：代码最后压缩，希望错误指定的是原来文件的位置，使用这个map实现；配置如下`devtool:source-map`即可

   1. 有很多配置方式：inline表示把map直接放到压缩好的js文件里面
   2. cheap则是仅指定行且不管loader或者第三方的错误
   3. module负责第三方文件错误
   4. eval是通过js里面的eval方法进行代码的映射
   5. 开发中使用: cheap-module-eval-source-map
   6. 线上则是cheap-module-source-map

5. Webpack-dev-server加速开发

   1. 一种方式是`webpack --watch`即打包文件变化则自动更新

   2. `webpack-dev-server`的方式，不用刷新且是服务器模式

   3. dev-server的配置，代码如下，使用base指定文件路径；使用open帮助自动打开和访问界面；proxy进行代理，配置请求；

      ```jsx
      devServer:{
        contentBase:'dist',
        open:'true'，
        proxy:{
          '/api':'http://localohost:8080'
        }
      }
      ```

   4. 可以在node中使用`compiler = webpack(config)`，然后调用webpack；命令行里面也可以调用

   5. 将内容放到内存，提高了速度；拿到dist就不是这个方式打包；

6. HMR

   1. 之前的环境是自动刷新来加载新文件，但是过于笨重；且模块之间会有影响没有状态储存的实现

   2. 使用`webpack.hotModuleReplacementPlugin()`插件，且在devServer里面设置`hotonly: true`和`hot:true`，hotonly是表示HMR失效的时候自动刷新(true表示不是自动刷新)，然后配合下面代码。即模块里面开启hot，当指定文件改变了，执行后面的回调函数进行更新；

      ```jsx
      if(module.hot){
        module.hot.accept('./path.js', ()=>{
          func()
        })
      }
      ```

   3. 上面的代码实际上css-loader或者vue和react脚手架都已经提前实现了；

7. Babel

   1. 一个问题就是浏览器不是都实现了ES6代码

   2. 官方文档很关键

   3. 使用的是`babel-loader`，一般来讲node_modules都做好了兼容，所以不用去翻译

   4. loader是一个编译器，实现转换如下配置，即进了编译，然后加入ES6到ES5的转换方式；presets里面两个参数，第一个是presets集合，第二个是配置项

      ```jsx
      rules:[
        test: /.\js$/,
        excludes:/node_modules/,
        loader:'babel-loader',
        options:{
        	presets:[['@babel/preset-env', {
        		useBuiltIns: 'usage'
        }]]
        },
      ]
      ```

   5. bable-polyfill解决的是一些Promise, map或者其他低版本浏览器没有的内置对象和内置方法；安装后然后import即可

   6. 希望polyfill实现按需注入，要按照上面的通过`useBuiltIns`进行实现

   7. `targets`函数进行浏览器型号的指定，这样能根据型号进行特定函数的转化

   8. 直接import是全局注入，会污染环境，代码如下；适用于第三方库的开发，正常的生产自然还是要注入的；

      ```jsx
      options:{
        'plugins':[['@babel/plugin-transform-runtime', {
          'corejs':2, 'helpers': true, 'regenerator': true, 'useESModules': false,
        }]]
      }
      ```

   9. 上面的corejs要安装`@babel/runtime-corejs2`

   10. .babelrc里面放的就是babel-loader里面的options配置，注意里面不能写注释的

   11. 执行方式是从下到上，针对react要额外多配置一个`@babel/preset-react`

---

## 高级内容

1. Tree-shaking：

   1. 希望做到部分引入，而不是全部注入到最后的js文件内

   2. 仅支持import，因为CMD是动态引入

   3. 开发环境下默认没有，要如下配置

      ```jsx
      optimization:{usedExports: true}
      ```

      然后在package.json里面进行配置，解决的是polyfill注入的问题，因为没有import但需要，所以避免tree-shaking；直接设置成false则是表明都要去做tree-shaking；

      ```jsx
      'sideEffects': ['@babel/polyfill', '*.css']
      ```

   4. 上面加入的css也是同理，因为css并没有export，这样tree-shaking之后是会删掉的

   5. 开发环境下，tree-shaking没有删除代码，仅是指出，适应了source-map；而生产下则是直接删除了；

2. 开发和生产环境

   1. 指定config的方式

   2. 利用merge的方式合并配置

      ```jsx
      module.exports = merge(webpackDev, webpackCommon);
      ```

3. code-split

   1. clean-webpack-plugin删除是根据config进行根目录来设置，可以如下的方式，额外的配置root

      ```jsx
      new CleanWebpackPlugin(['dist'], {
      	root:path.resolve(__dirname, 'yourPath')
      })
      ```

   2. 解决的问题是：第三方库不应该打包进去，或者不改变的模块希望抽离出去，这样client拿文件的时候，采用多请求的方式就可以加速，且相同文件直接采用缓存的方式；webpack中直接开启就好`optimization:{spiltChunks: {chunk:'all'}}`

   3. 也可以通过动态import的方式`import('package').then()`这种方式去做；需要安装`babel-plugin-syntax-dynamic-import`去支持动态import的语法。然后在bablerc里面的plugin字段添加上去；

   4. 是可以采用魔法注释的方式进行动态import库命名`import(/*webpackChunkName:"lodash"*/ 'lodash')`

   5. splitChunks里面各项含义

      1. chunks，进行代码分割；'async'是只负责异步加载的代码，'all'表示同步和异步的都有

      2. cacheGroup：具体结构如下

         ```jsx
         cacheGroup:{
         	vendors:{
         		test:/[\\/]node_modules[\\/]/,
         		priority:-10,
         		filename:'vendors.js',
         	}
         }
         ```

         