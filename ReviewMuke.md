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

      1. 















































































