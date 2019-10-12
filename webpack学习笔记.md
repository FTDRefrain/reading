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

      2. cacheGroup：具体结构如下，filename指定打包的名字，不指定就是`vendors~main.js`，是群聚名字，即一个规则匹配的压缩到一个群组里面；automaticNameDelimiter指定文件中间分隔符名字；default指定的是其他文件;本身是对chunks规则的一个补充

         ```jsx
         cacheGroup:{
         	vendors:{
         		test:/[\\/]node_modules[\\/]/,
         		priority:-10,
         		filename:'vendors.js',
         	}，
           default:{}
         }
```
         
      3. 参数相关解释
      
         1. minSize指定的是，小于这个值的包就不进行代码分割了；
         2. maxSize则是打包出来的文件进行二次分割；
         3. minChunks：即引入几次才进行代码分割
         4. maxAsyncRequests: 即最高的请求数限制，打包出来的文件多于这个参数就不进行分割了；
         5. maxInitialRequests：即首页的引入文件数量
         6. Name: true即表示cacheGroups里面的名字有效
         7. cacheGroup: 即符合要求的先不打包，先分配组，然后文件扫完之后才进行打包；解决的是想多个文件打包到一个里面的情况；
         8. Priority：即各组的优先级
         9. reuseExistingChunk：true；因为存在一个包已经放到group里面，但是另外group使用到了，这时候就不用放进去，而是直接使用另一个组的包就行
   
4. 懒加载

   1. 本身就是异步加载的形式

   2. 必须使用polyfill去注入Promise实现异步的方式

   3. 使用方式的样例

      ```jsx
      async function getEle = () => {
        const {default:_} = await import(/*webpackChunkName:"lodash"*/ './lodash');
        return _
      }
      getEle.then(_ => log(_))
      ```

5.  分析和优化

   1. 分析工具:`webpack/analyse`工具直接进行分析
   2. `--profile --json`生成一个打包文件的描述
   3. 或者在code split -> bundle analysis找到对应的分析工具
   4. Prefetching
      1. Chrome里面使用cmd+shift+P进行命令查找，然后调出coverage，进行屏幕操作的录制
      2. 考虑的是代码的使用率，希望首屏时候代码使用率更高
      3. 提出的一个思想就是交互产生的内容变成异步加载，也就是webpack推荐的chunks: async的原因，即异步加载的去split才有效果；而现在可以做到网络带宽有限制的时候就去加载；
      4. 通过`import(/*webpackPrefetch:true* './click.js'/).then`的方式进行prefetch标签的注入
      5. 可能在浏览器会有兼容的问题
   5. Preload：即和主业务文件一起进行加载

6. CSS文件

   1. Output -> chunkNames，指定的是entry以外引入文件打包之后的名字
   2. Mini-css-extract-plugin，本来CSS是在js文件里面，希望还是样式抽离出去
   3. 首先是rules里面的`loader: MiniCSSExtractPlugin.loader`，然后注意配置package.json里面的sideEffects字段，避免tree-shaking将内容刷掉
   4. 在plugin里面则是进行打包名字的配置，同样直接引入进filename，间接的进chunkFileName
   5. Optimization-css-assets-webpack-plugin，进行样式的压缩和合并
   6. cacheGroup里面可以额外配置，结合官网文档看；

7. 浏览器缓存文件的问题

   1. 打包出来的文件名字没有加hash的话，web访问时候默认走缓存；

   2. 老版本里面，可能存在没有更新但是打包出来的文件hash内容不同的情况。采用如下的配置

      ```jsx
      optimization: {runtimeChunk: {name: 'runtime'}}
      ```

   3. 上面的配置放到新版本也可以

   4. Runtime: 逻辑和第三方库的关系放在mainfest里面，本身写在打包之后的文件里面，这个内容可能会发生改变，使用上面的配置，mainfest放到了runtime文件里面，这样就不会发生失效

8. Shimming，垫片的思想

   1. 一个问题是，本身是模块化编写和打包，这样每个文件都要进行引入，哪怕变量是全局的也不行。即如$, _这样的虽然全局注入，但是不能用

   2. 自己的可以加import，第三方就没有办法，解决方案如下；且可以指定方法，如_join代表的是lodash.join

      ```jsx
      new Webpack.ProvidePlugin({
        $: 'jquery'，
        _join: ['lodash', 'join'],
      })
      ```

   3. 模块里面的this不是window，改变指向的方式如下

      ```jsx
      loader: 'imports-loader?this=>window'
      ```

   4. 即shimming的思想就是人为的修改或者做一些修饰

9. 环境变量

   1. `webpack --env.production=true`的方式进行参数的传递，然后配合下面的方式，进行config的配置和导出

      ```jsx
      module.exports = env => env.production
      ```

---

## 实战内容相关

1. Library

   1. 自己构建库：使用package.json配置内容，设置下面的参数；直接pro模式打包文件

      ```jsx
      output: {library: 'root', libraryTarget:'umd'}
      ```

   2. 通过library指定全局注入变量的名字，umd的方式适配所有的包引入的情况

   3. externals字段配置一些不需要的包，即第三方不希望打包其他的第三方库，避免重复打包的方式，配置如下，指定引入的名字

      ```jsx
      externals: {lodash: 'lodash'}
      ```

   4. npm注册账号，`npm adduser`然后进行'npm publish'之后实现包的打包发布；

2. PWA配置相关，即离线访问

   1. `http-server`进行服务器的开启，指定文件或者文件夹都可以；

   2. chrome里面在application里面，service worker配置项进行缓存的清除；

   3. `workbox-webpack-plugin`进行PWA的配置，具体如下；之后生成了precache和service-worker文件

      ```jsx
      new Workbox.GenerateSW({
        clientClaim: true,
        skipWaiting:true,
      })
      ```

   4. 配置结束要加上业务代码，具体如下

      ```jsx
      if('serviceWorker' in navigator){
        window.addEventListener('load', () => {
          navigator.serviceWorker.register('./service-rigester.js')
            .then(r => {
            	func()
          }).catch(e => log(e))
        })
      }
      ```

3. TypeScript打包配置，本身是要安装使用的；

   1. 后缀是tsx，`loader: ts-loader`

   2. 构建tsconfig.js文件进行相关的设置，简单示例如下；即指定打包文件，模块引入方式，导出方式和是否许可引入js；

      ```jsx
      {
        "compilerOptions":{
          "outDir": "./dist",
          "module": "es6",
          "target": "es5",
          "allowJs": true
        }
      }
      ```

   3. `@types/lodash`安装后是在ts里面使用lodash也进行方法和类型的检验

   4. 对应库的类型文件要去搜索安装才可以配合ts使用；

4. DevServer实现请求分发

   1. 因为开发时候url不固定，为了方便之后的更改采用的是相对路径

   2. 一个方式是采用charles或者fiddler进行代理服务器转发请求的方式进行设置

   3. 可以去配置devServer，例子如下；根路径`/`的代理默认是不支持的，要额外配置`index: ''`才可以

      ```jsx
      proxy: {
        'proxy/api': {
          target: 'http://proxy.server.api',
          pathRewrite: {'header.json': 'demo.json'}
        }
      }
      ```

   4. 上面做到了：实现访问代理；路径重写

   5. 本身的devServer针对的是开发环境

   6. https的代理要额外配置`secure: false`才行

   7. 可以使用bypass字段进行请求修饰或者拦截，后面跟的是func

   8. `changeOrigion: true`，因为有的web会做域名访问限制，这个字段能突破限制

   9. 本身是基于`http-proxy-middleware`这个中间件；有很多的配置，且直接在`proxy`字段下面进行配置，如`headers`相关字段

5. devServer解决SPA路由问题，这种方式仅开发时候有效，线上还是要用nginx或者apache修改

   1. React-router-dom实现SPA路由；使用brower的方式，一个问题是单页面应用是只有index.html的，所以直接访问如`host://123`的页面是找不到123这个文件的
   2. 配置如下`devServer: {historyApiFallback: true} `，就是所有的api都打回/
   3. 本身底层使用`connect-history-api-fallback`写的，还有一些rewrite字段，进行动态路由的转发

6. EsLint在webpack中的配置

   1. 安装后`npx eslint --init`进行配置，本身可以使用流行，自己设置，文件导入三种方式
   2. 流行里面，有airbnb, standard, google三种方案，一般是第一种
   3. 生成的位置在eslintrc.js文件里面
   4. 对于react的配置要额外多加一个jsx解析，配置如下`parser: babel-eslint`
   5. 不同的规范，设置rules字段，根据IDE提供的规范名字进行指定，变成0就是直接去掉
   6. 全局变量的问题`globals: {document: false}`就可以使用全局变量了；
   7. webpack里面借助loader实现，改变rules字段，`rules: {use : ['bable-loader', 'eslint-loader']}`
   8. devServer里面设置`overlay: true`这样，打包过程的错误会直接显示到网页上面，避免了IDE插件无法加入的影响； 
   9. 里面可以使用`force: 'pre'`进行loader的优先级强制提高

7. webpack优化方案

   1. 提高node, npm, yarn版本的更新

   2. 尽可能少的模块上面使用loader；

      1. 一种方式是`include: path.resolve(__dirname, '../src')`即指定文件夹

   3. 明确plugin的使用环境，如压缩，开发不用发行使用

   4. 使用官网推荐的插件，效率可以得到保证

   5. 对jsx做兼容，即`/\.jsx?$/`的方式

   6. resolve的作用`extensions: ['.js', '.jsx']`即自己进行扩展名的引入；本身基于node的文件系统，有性能损耗

   7. resolve另一个作用是`mainFiles: ['index', 'child']`对于文件夹的引入直接去找index然后再找child

   8. 设置alias进行路径的设置

   9. 上面的resolve就不要使用，因为耗性能

   10. 希望第三方库在开发时候只有第一次打包

       1. 可以将第三方库提前打包好，如下配置生成了dll文件以及mainfest用来之后开发环境下的dll缓存；library目的是全局注入，方便之后的import；

          ```jsx
          entry: {vendors: ['react', 'react-dom', 'lodash']},
          output: {
            filename: '[name].dll.js',
            path: path.resolve(__dirname, '../dll'),
            library: '[name]'
          },
          plugins: {
            new webpack.DllPlugin({
              name: '[name]',
              path: path.resolve(__dirname, '../dll/[name].mainfest.json'),
            })
          }
          ```

       2. 然后使用`add-asset-html-webpack-plugin`帮助引入一些静态文件，将上面打包出来的vendors.js文件注入到index模板里面

       3. `new webpack.DllReferencePlugin({mainfest: path.resolve('./vendors.mainfest.json')})`，这样在import的时候，发现全局变量有且有mainfest依赖，就不会重复打包而是直接使用

       4. 原来的方式是将第三方库都打包放到了cacheGroup里面

   11. 控制包大小，主要就是tree-shaking

   12. 多进程打包：`thread-loader`, `parallel-webpack`, `happypack`三种方式

   13. sourceMap的合理配置来优化

   14. 线上工具进行stat分析进行优化

   15. 开发环境的内容编译，没用插件剔除

8. 多页面打包

   1. 核心就是通过html-webpack-plugin不断的注入模板，通过chunks指定引入文件的名字
   2. 具体代码也是遍历文件然后注入；

---

## 底层原理相关

1. loader编写
   1. 使用`loader-utils`进行参数的获取，一定是function这样，因为会有this绑定的问题
   2. `resolveLoader`进行loader查找路径
   3. `callback = this.async()`进行异步操作的声明
2. plugin编写
   1.  本身是类，通过`constructor(options)`获取到出入的参数
   2. `apply(compiler)`定义操作方法
   3. `compiler.hook`调用不同时段的钩子，找文档确定操作的时间
   4. compilation里面进行操作；异步的周期使用`tapAsync`且之后要cb往下走；同步周期则是使用`tap`注入内容，且没有cb；
   5. `node --inspect --inspect-brk node_modules/webpack/bin/webpack.js`开启node的调试模式，通过web可以进行断点调试；`debugger`关键字手动打断点；
3. bundler
   1. `fs.readFileSync`读取文件
   2. `@babel/parser`进入babel里面的ast分析工具，然后`parser.parse`获取到ast内容
   3. 使用`@babel/tarverse`进行ast的遍历，获取到目的节点，得到文件的依赖
   4. `@babel/transformFromAst`将ast根据`presets`做一次语法转译，然后变成代码
   5. 代码生成部分，因为只是进行了语法的转译，本身client还是做不了；通过闭包的方式实现，里面注意两点，一个是实现require方法，一个是声明exports，且要实现相对转换成绝对路径
   6. 通过eval执行代码

---

## 脚手架内容，通过看脚手架学习webpack配置

1. `CreateReactApp`脚手架，配置更灵活
   1. 线上环境
      1. `eject`弹出配置
      2. `[].filter(Boolean)`将前面所有的false都滤掉；
      3. `devtoolModuleFilenameTemplate`帮助定位sourceMap真正位置，从而帮助调错
      4. `TerserPlugin`负责进行线上环境js代码的压缩
      5. `resolve: plugin`里面依旧可以配置插件，即引入模块的时候额外进行一些操作
      6. `oneOf`字段，即文件匹配到一个loader就停止匹配了
      7. scss和sass本身都是有支持的
      8. 做了一些大小名字写错依旧能引入包的操作
   2. 开发环境：主要的`webpack-devServer`的配置
2. `Vue-CLI 3`脚手架相关，配置更简单
   1. 本身没有eject的过程
   2. 采用的是`vue.config.js`结合自己官网的api进行自定义配置
   3. 在`vue-cli-service-lib-config`里面进行了自己参数和webpack参数的转换；
   4. `configureWebpack`直接去写webpack配置，本身是调用`webpack-merge`进行了融合；