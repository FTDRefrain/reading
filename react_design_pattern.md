## 读书笔记-react设计模式
1. 第三章，开发高可用的组件
    1.  所有的绑定放到构造函数里面进行绑定，对性能好：主要的问题在于子组件的渲染上面，因为绑定的时候props要重新传一次；
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
    1.         
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        