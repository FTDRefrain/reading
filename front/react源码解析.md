1. 基础知识及React API

   1. 16以后已经实现了异步渲染；

   2. Babel playground实现实时的网上代码转换；

   3. 编译的时候，如果组件的名字是小写就按照字符串传入，否则传入变量，也就是为什么自定义组件一定要大写起手

   4. creatElement内容

      1. 从react.js里面看出是从./ReactElement里面引入的
      2. 抽出了所有的props且排除了key, ref, _self, _serouce四个props；
      3. 拿到所有的children，放到了props.children里面
      4. defaultProps处理，这里使用了是否是undefined，即null不会使用default；
      5. 将上述预处理之后的内容给ReactElment

   5. ReactElment

      1. 注意$$typeof存放了组件的类型，大部分都是`REACT_ELEMENT_TYPE`但是会有一部分不是；
      2. type用来存放该组件是class还是function还是其他一些类型

   6. Component

      1. ReactBaseClasses文件里面
      2. 本身是一个function，传入props, context, updater三个参数，updater用来分辨是react还是native，来调用不同的方式进行更新；
      3. pureComponent多加了一个字段用来判断当前组件是否是pure，用给以后的DOM更新；

   7. ref使用

      1. 三种方式：string，回调和React.createRef；string不推荐，马上废弃

      2. createRef就是直接返回了一个对象`{current: null}`，绑定之后，就能通过current获取到ref，但是function组件不能使用，因为本身不是实例没有this；

      3. forwardRef：

         1. 两点，一个现成的包不知道是什么类型的，ref有风险，另外使用HOC的时候，比如redux，拿到的ref并不是想要的ref

         2. 使用方式如下，这样高阶里面就能获取到内容了；

            ```jsx
            const TargetComponent = React.forwardRef((props, ref)=>(
            	<div props={props} ref={ref} />
            ))
            ```

         3. 方法返回的是一个对象`{$$typeof:REACT_FORWARD_REF_TYPE, render}`注意这里面的typeof和之前的不一样，因为返回的这个组件会作为type传入的createElement里面，而create方法本身依旧还是返回之前说的$$typeof；

   8. context内容

      1. 新API使用createContext拿到Provider和Consumer；

   9. ConcurrentMode

      1. 为渲染任务添加优先级，使用方式如下，中间的内容就是最低优先级的渲染方式

         ```jsx
         <ConcurrentMode>
         	<MainComponent></MainComponnet>
         </ConcurrentMode>
         ```

      2. React-dom里面的flushSync会强制提高优先级，本身是对方法的，比如setState，使用的时候直接包裹就行；

      3. 比如动画和更新数据放在一起，会变得卡，但是通过提高数据变换的优先级分开执行就会流畅一些；

      4. 本身就是一个symbol

   10. Suspense

       1. 使用的是部分的功能，流程代码如下，下面写的数据加载功能大概要19年6月后才能使用；

          ```jsx
          let data = '';
          let promise = '';
          function requestData(){
            if(data) return data;
            if(promise) throw promise;
            promise = new Promise((resolve)=>{
              setTimeOut(()=>(
              	data = 1
                resolve()
              ), 2000)
            })
            throw promise
          }
          
          const MainCom = () =>{
            let data = requestData();
            return <p>{data}</p>
          }
          // 下面可以包裹多个组件，只要有一个是promise，整个内容就还是callback
          <Suspense calllback="loading data">
          	<MainCom></MainCom>
          </Suspense>
          ```

       2. 本身可以配合使用react里面的lazy进行组价的异步加载

          ```jsx
          const LazyCom = lazy(()=>import('./lazy.js'))
          ```

       3. Suspense本身还是symbol

       4. lazy返回的是自己的type，传入的组件（thenable对象处理后），status和result，其中status是-1表示pending状态；

   11. Hooks

       1. 解决事件绑定方法，因为func类型每次都会执行，后面给一个[]后就不会改变了，完美模拟了Unmount；

          ```jsx
          useEffect(()=>{
            bindFunc();
            return ()=>{
              unbindFunc();
            }
          }, [])
          ```

   12. Children

       1. 有map和foreach两种展开方式：一个是返回新的数组，一个是返回原来的数组；展开是完全平铺，如果后面是`[a, [a, [a ,a]]]`的方式就会变成`[a,a,a,a]`的方式
       2. 过程看具体的流程图；
       3. 里面的对象池的作用：因为生成和释放对象耗费性能，所以先构建好10个对象，通过拿出记录值和手动设定成null放回的两个过程实现；

   13. Others内容补充

       1. memo和pure差不多的内容
       2. Fragement，直接使用<>的方式也可以，babel会直接进行编译
       3. Strict_Mode会表示一下内部使用上的问题，比如API过时等；
       4. CloneElement，复制一个组件

2. 更新部分

   1. render
      1. 更新方式有三种
         - ReactDOM.render || hydrate
         - setState
         - forceUpdate
      2. hydrate和render的区别就是在于里面的第四个参数一个是true一个是false，因为hydrate用于SSR，里面的true表示是否要复用DOM节点，所以SSR的时候采用复用的方式会提高性能；
      3. 对于render，会判断当前节点是否有子节点，如果有的话是要hydrate的；如果没有就直接删除整个root；构建了一个rooter（作用是创建fiberRooter对象），然后创建expireTime对象，update对象，然后放到root上面
   2. fiberRoot
      1. 拥有自己的数据结构

























































