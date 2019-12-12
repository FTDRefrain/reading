## 读书笔记-react设计模式
1. 第三章，开发高可用的组件
    1. 所有的绑定放到构造函数里面进行绑定，对性能好：主要的问题在于子组件的渲染上面，因为绑定的时候props要重新传一次；
    1. 无状态组件：
        1. 可以接受props,没有this也没有声明周期函数，可以使用ref但本身没有
        1. 没有声明周期，所以数据更新时候无法进行检测也就少一部分的优化
    1. setState本身接受第二个参数，是一个回调函数，在setState执行完毕以后立即执行的函数；    
    1. 只要是能根据props计算的值就不应该保存在state里面，而是通过一个方法直接返回计算的值：因为子组件的构造之后做一次，直接公式写里面是  
    是不会更新的
    1. 没有参与渲染的数据不要放进去，使用类的私有字段保存就行；     
    1. docgen库，可以把设置好的props的类型和描述打印成JSON字段，这样能构成准则；    
    1. 组件复用，比如列表类型的组件都是通过map将里面的内容展示出来，所以将结构抽出来，使用props的方式将  
    需要的内容穿进去就能实现代码的复用了；    
1. 第四章，组合一切        
    1. 拆分组件：容器组件+表现组件
        1. 容器组价：        
            1. 更关心行为部分
            1. 负责渲染对应的表现组件
            1. 发起API请求并操作数据
            1. 定义事件处理器
            1. 写作类的形式
            1. 例子如下
                ```javascript
                   render(){
                       return(
                           <Container {...this.state} />
                       )
                   }
                ```
        1. 表现组件
            1. 更关心视觉表现
            1. 负责渲染HTML标记
            1. 以props的形式从父组件接受数据
            1. 通常写作无状态函数形式
            1. 例子如下
                ```javascript
                   const Geolocation = ({ latitude, longitude }) => (
                             <div>
                               <div>Latitude: {latitude}</div>
                               <div>Longitude: {longitude}</div>
                             </div>
                           )
                ```
        1. 高阶组件，将组件传进去，在render里面类似上面展开state和props
            ```javascript
               const withInnerWidth = Component => (
                 class extends React.Component { ... }
               )
            ```
        1. 尽量还是少用高阶组件，因为添加新的渲染函数，生命周期和内存分配；
        1. 函数子组件
            ```javascript
            const FunctionAsChild = ({ children }) => children()
            // 使用方式如下
            <FunctionAsChild>
                  {() => <div>Hello, World!</div>}
             </FunctionAsChild>
            
            ```
1. 第五章，恰当的获取数据
    1. 例子如下
        ```javascript
           const withData = url => Component => (...)
           const withGists = withData(
               props => `https://api.github.com/users/${props.username}/gists`
           )
           // 下面是高阶组件里面的方法，因为有使用url参数的情况，所以通过和上面结合的方式对url部分使用props里面的值
           componentDidMount() {
                 const endpoint = typeof url === 'function'
                   ? url(this.props)
                   : url
                 fetch(endpoint)
                   .then(response => response.json())
                   .then(data => this.setState({ data }))
           }
           // 之后在最外面的组件添加name就可以了
           <ListWithGists username="gaearon" />
        ```        
    1. react-refetch库，可以进行上述同样的效果且更好，主要使用connect将数据和函数放在一起；  
    还有pending和fulfilled两个状态可以监控数据获取阶段；
1. 第六章，表单，事件，动画和SVG；
    1. input使用时候，可以绑定name，这样通过target.name就可以拿到对应的input框了；
    1. setState里面使用传入的变量作为名字的时候，可以通过[variable]的方式；
    1. 合成事件：组件的点击事件是一个合成事件，可以绑定相同的方法，通过t.type的方式获取到当前触发事件的名字，通过switch的方式就能实现同一个方法处理不同事件；react事件处理，本质上是利用冒泡机制，通过在根部监听所有的事件；
    1. ref本身可以获取到自定义的组件，然后调用该组件的方法，这样就能在父组件使用子组件的方法；缺点就是当子组件里面的名字改变的时候，所有使用该名字的父组件都要跟着变；
1. 第七章，CSS
    1. 行内的优势：和数值相关的内容或者不同情况下的样式可以通过变量去进行控制；缺点：不好调试，代码体积大，部分功能不全；
    1. modules，里面通过:global的方式使得后面的样式是全局而不再是局部变量；通过composes的方式可以引入当前或者外部依赖中已经声明的样式；
    1. 原子级CSS，即类似BS的方式，大小和样式直接放到类名字上面，通过composes的方式组合；这种方式类似行内样式的声明，即用变量去控制；
    1. style-component和SCSS的方式
1. 第八章，SSR
    1. 首先应该先完成客户端，且在需要SEO或者没有其他优化方式的时候再采用SSR的方式；
        
        
        
        
        
        
        
        
        
        
        
        
        
        
