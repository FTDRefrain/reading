一.浅谈react

1. Web component = html template + custom element(自定义组件) + shadow dom(样式和作用域) + html import(其他的引入方式)
2. DidMount和DidUpdate时候，DOM节点才是真的挂载上去；可以使用React.findDOMMode进行获取，拿到的就是render里面返回的东西；
3. React.cloneElement进行子组件的复制
4. componentWillReceiveProps：当props有目标key时候直接更新
5. in用来看是否存在key
6. Props.children获取子组件内容
7. 多的东西使用switch而不是if else做

---

二. 漫谈react

1. 事件委派和自动绑定(class和纯函数里面就没有了)，箭头函数是自动绑定this

2. 原生事件不要和react事件混用

3. 表单相关：

   1. input：使用的是value和onChange事件；通过type控制checkbox这种；ratio和checkbox使用checked

   2. textarea：和上面一样

   3. select：value和multiple控制，也是onChange事件；下属option使用selected

   4. 受控组件，即通过value和onChange事件绑定的

   5. 非受控组件，使用的是defaultValue和defaultChecked

   6. 对象里面如下是使用变量，这样就能统一使用一个事件去绑定

      ```jsx
      {[name]:'123'}
      ```

4. CSS模块化：

   1. 通过webpack里面的css-loader实现；可以使用:global声明是全局的；

   2. composes去进行复用，即一个类里面引入其他的类

   3. 变量使用`$default-size: 40px`，可以复用，使用calc进行计算

   4. 样式覆盖方法，使用的是data-role的方式，还是写在导出的classes里面

      ```css
      // dialog.css
      [data-role = 'dialog-root'] {
        // override style
      }
      ```

   5. 配合webpack里面的exclude和include进行公共模块和私有模块的分离，使用modules关键字区分是否进行模块化

   6. sourceMap即进行压缩后代码位置信息记录，这样报错就能找到位置了；

   7. CSS动画

      1. 一种是使用样式的animation
      2. 一种是使用CSSTransition
         1. 首先是TransitionGroup作为最外层将CSSTransition包裹住
         2. CSSTransition：
            - in表示是否隐藏
            - timeout进出的延迟，`{exit: 300, enter: 500}`这样
            - classNames去设置动画的类名，配合enter, exit实现两个阶段
            - unmountOnExit，移除处于隐藏状态的元素，一般为true
            - appear, 是否初次渲染即有动画
            - key配合上面的group进行优化的，配合路由切换的时候使用location.pathname
         3. 使用router里面的switch组件做到路由切换，location绑定当前router里面的location，否则有重复请求。

5. 组件通信，P/C这种在没有交叉环境下就是必用的

6. 组件抽象方式

   1. Mixin，实际上是多重继承，往目标原型上挂载；JAVA利用接口实现；问题很大，变量名重复，内部数据难维护，多依赖问题

   2. 高阶组件：

      1. 属性代理：直接使用传入的组件，再将props填进去

         ```jsx
         const MyContainer = (WrappedComponent) =>
           class extends Component {
             constructor(props){
               super(props)
               this.state = {}
             }
             handleClick(){}
            	render() {
               const newProps = {
                 state:this.state,
                 handleClick:this.handleClick
               }
               return (
               	<WrappedComponent {...this.props} {...newProps}/>
               )
             }
           }
         ```

      2. 反向继承：即继承传入组件然后使用传入组件的render；可以做渲染劫持和数据控制，即修改elementTree

         ```jsx
         const MyContainer = (WrappedComponent) => 
         	class extends WrappedComponent {
             render(){
               const elementTree = super.render()
               const newProps = Object.assign({value: '123'}, elementTree.props)
               const newElementTree = React.cloneElement(elementTree, newProps, elementTree.props.children)
               return newElementTree
             }
           }
         ```

      3. 组件命名：要手动设置displayName

         ```jsx
         static displayName = `HOC(${getDisplayName(WrappedComponent)})`
         const getDisplayName = (Wc) => {
           return Wc.displayName || Wc.name || 'component'
         }
         ```

      4. 外面在多一层去传递参数

   3. 纯函数和PureComponent

      1. 纯函数特征：

         1. 无副作用即不改变外部变量
         2. 相同输入相同输出
         3. 没有外部依赖，即不适用共享变量

      2. Immutabel解决引用比较和深度搜索的问题

         1. 本身数据不可变；引用对象的赋值使用的是可共用的节点；

         2. 操作时候使用fromJS和toJS使用原生的操作方式会舒服一些，另外比较的时候使用Immutable.is去比较，原生才是===比较

         3. Cursor，给深度嵌套的元素加上标记，好找也好更新

            ```jsx
            let data = Immutable.fromJS({a:{b:{c:1}}})
            let cursor = Cursor.from(data, ['a', 'b'], newData => {
              doSomething()
            })
            cursor.get('c') // 1且会执行doSomething
            cursor = cursor.update('c', x => x+1) //特定位置更新
            cursor.get('c') // 2
            ```

7. 测试及优化

---

三. vDOM内容相关

1. createElement：根据js对象构建虚拟节点

2. instantiateReactComponent：根据节点类型，调用不同的React-component去构建实例

   1. text文本类型：ReactTextComponent
      1. counstructor进行变量初始化
      2. mountComponent第一次挂载
      3. receiveComponent组件更新
   2. ReactDomComponent：进行节点的构建，方法同上面三个部分
      1. 属性的更新：事件代理，样式，属性
      2. 子节点的更新

3. 生命周期

   1. 组件本身是有限状态机，通过管理状态实现组件的管理

   2. 实际初始会有getInitialState和getDefaultProps两个周期，下面两个声明对应这两个周期

      ```jsx
      this.state = {}; static defaultProps = {}; 
      ```

   3. createClass方法

      1. 构建组件，没有构造，使用的是getInitialState或者componentWillMount启动的构造
      2. defaultProps则是getDefaultProps里面构建
      3. 最后得到一个公共类

   4. MOUNTING阶段一

      1. 无状态不用更新队列
      2. 有状态：willMount时调用setState则是先合并一下state，render时候再正式渲染。
      3. 上面两个有DidMount则执行
      4. 有错误则unMountComponent后才挂载
      5. 递归下去，当然现在是Fiber架构

   5. RECEIVE_PROPS阶段二

      1. componentWillReceiveProps时候state也是合并后异步更新的
      2. 先看类型是否相同->是否有receiveProps->最新state插入更新队列->根据更新队列和SCU看是否更新->更新里面多了willUpdate和DidUpdate两个周期，不更新则是直接将props和state存住

   6. UNMOUNTING阶段三

      1. 同样setState放到异步队列里面，直接所有状态清空

   7. setState相关

      1. 本身分为同步和异步两种方式，直接调用就是异步的，但是在web api那边走一下就是同步的了

      2. 异步下多次操作会被合并，实际上就加了1次，因为拿到的id就是初始的id；如果是变量承接然后多次操作是可以的

         ```jsx
         this.setState({id:this.state.id+1})
         this.setState({id:this.state.id+1})
         ```

   8. 事务：对方法进行了wrapper，先执行所有wrapper的initialize，然后是transcation.perform调用目标方法，然后是所有的wrapper的close方法；和洋葱不同，事务顺序是固定的；

4. Diff算法补充一点：list里面的key用来实现移动；规定从左向右移动，这样newIndex < oldIndex就不用动了，但问题就是新内容里面首位到了末位；

5. Patch将上面的diff算法得到的更新队列，一个for循环进行操作；因为队列里面存的就是每步执行方法

---

四. Flux架构

1. 