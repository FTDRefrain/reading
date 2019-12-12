## Hook专题

1. 组件状态
   1. useSetState
      1. 即一次声明多状态的时候，只是想更新部分内容的时候使用。
      2. 利用的是setState接受回调参数的特性，然后浅拷贝进行复制
   2. useReducer，即局部的redux
   3. 依赖里面，dispatch, setState, ref.current这可在useEffect, useMemo, useCallback里面安全的使用，不会因为父组件的渲染导致函数的重新声明从而更新子组件
   4. useRef获取当前值，即使用的时候拿的是ref.current
   5. useEffect是每次render之后执行
2. 声明周期相关
   1. componentDidUpdate模拟：useRef上面多挂载一个mounted，用来进行第一次render的标识
   2. didCatch做不到
3. 事件处理
   1. 双向绑定，只要给一个值就可以
   2. 事件监听和销毁
4. 其他
   1. 表单
   2. 路由api支持
5. 副作用封装
   1. 将节流和防抖两个函数整个封装起来，返回状态和状态切换函数
   2. Navigator获取用户状态，如onLine开是否在线
6. 副作用衍生
   1. 设定依赖，依赖发生改变之后调用effect进行设定。一个例子就是title。
7. 代码封装
   1. promise请求
   2. 滚动加载
   3. 