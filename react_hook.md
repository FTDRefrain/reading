## Hook专题

1. 组件状态
   1. useSetState
      1. 即一次声明多状态的时候，只是想更新部分内容的时候使用。
      2. 利用的是setState接受回调参数的特性，然后浅拷贝进行复制
   2. useReducer，即局部的redux
   3. 依赖里面，dispatch, setState, ref.current这可在useEffect, useMemo, useCallback里面安全的使用，不会因为父组件的渲染导致函数的重新声明从而更新子组件
   4. useRef获取当前值，即使用的时候拿的是ref.current
   5. 