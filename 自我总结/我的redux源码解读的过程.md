## 项目中创建store的过程及redux核心API的源码解读
  1. 创建store 

  ```javascript
    const store = createStore(
      combineReducers(allReducer), 
      compos(applyMiddleware(中间件thunk，logger...))
    )
  ```

  - `createStore`方法，传入项目中所有reducer的集合
    - `combineReducers`（所有reducer集合）返回一个方法`combination`，接受两个参数，`state` 和 `action`
    - `compos`注册中间件。会`store`和`dispatch`传给中间件，让其自由发挥。`applyMiddleware`将中间件与redux进行对接的，(例如`thunk`中间件)将dispatch进行一层封装。其目的是将`action`既能是对象也能是方法。
    - 执行一次无法匹配的action，目的是初始化每个reducer管理的状态
  
  2. dispatch 与 getState
  ```javascript
    currentState = currentReducer(currentState, action)
    
    // 该返回值的数据结构为reducer名为值，value为这个reducer管理的状态值
    // { 
    //  testReducer : {
    //     a : 1 ,
    //     b:[{name:'JC.Liu' , age:'24' , sex:1}] 
    //   }
    // }
  ```
 
  - 执行时传入action对象，`currentReducer`为上面的`combination`。并返回一个所有状态的对象，供给`getState`方法。

  ```javascript
    function getState(): S { return currentState as S }
  ```

  ```javascript
    // 在其中遍历所有的子项reducer，
    for (let i = 0; i < finalReducerKeys.length; i++) {
      const key = finalReducerKeys[i]
      const reducer = finalReducers[key]
      const previousStateForKey = state[key]
      // 传入子项reducer自己管理的状态和action，在自己的reducer中进行操作，返回更新后的状态
      // 在自己的reducer中可自行发挥，输出到最后更新的状态
      const nextStateForKey = reducer(previousStateForKey, action)
      if (typeof nextStateForKey === 'undefined') {
        const errorMessage = getUndefinedStateErrorMessage(key, action)
        throw new Error(errorMessage)
      }
      nextState[key] = nextStateForKey
      hasChanged = hasChanged || nextStateForKey !== previousStateForKey
    }
  ```
 
  - 更新store中的值
  ```javascript
    const listeners = (currentListeners = nextListeners)
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i]
      listener()
    }
  ```

  对每个订阅者进行遍历更新，`nextListeners`指向的真实订阅者在 `react-redux connectAdvanced.js`文件中的`checkForUpdates`，其目的就是更新store中的值。
 
 3. react-redux Provider
  - 项目中顶层结构会存在一个`Provider`，不用怀疑是其他的什么东西。实际上就是`React.createContext`中的`Provider`。
  ```javascript
    return <Context.Provider value={contextValue}>{children}</Context.Provider>
  ```
 4. 组件通过 connect 获取store中的状态
  ```javascript
    const mapStateToProps = (state) => state
    const mapDispatchToProps =  { ...actions }
    connect(mapStateToProps, mapDispatchToProps)(Test)
  ```
  - 使用时传入`mapStateToProps`返回的状态和`mapDispatchToProps``action`的集合。
  - connect -> connectHOC -> connectAdvanced -> wrapWithConnect
    在`connect(mapStateToProps, mapDispatchToProps)`中执行`connect`方法，将参数继续往下传递给`connectHOC`，`connectHOC`实际上就是`connectAdvanced`方法，执行后返回`wrapWithConnect`，该方法接受一个组件。其中进行实际的动作。
    查看`ConnectFunction`方法，最后返回的`renderedChild`。往上追溯，
    
  ```javascript
    const renderedChild = useMemo(() => {
      // shouldHandleStateChanges 来源与connect第一个参数值的Boolean判断，有传入值则为true
      if (shouldHandleStateChanges) {
        return (
          <ContextToUse.Provider value={overriddenContextValue}>
            {renderedWrappedComponent}
          </ContextToUse.Provider>
        )
      }

      return renderedWrappedComponent
    }, [ContextToUse, renderedWrappedComponent, overriddenContextValue])
    
    // *************************************
    // 继续往上追溯 `renderedWrappedComponent`
    // *************************************

    const renderedWrappedComponent = useMemo(
      // WrappedComponent 为传入的组件 
      () => <WrappedComponent {...actualChildProps} ref={forwardedRef} />,
      [forwardedRef, WrappedComponent, actualChildProps]
    )

    // *************************************
    // 继续往上追溯 `actualChildProps` -> 
    // childPropsSelector ->
    // createChildSelector -> 
    // *************************************

    // selectorFactory 在connect方法中向下传递的回掉
    function createChildSelector(store) {
      return selectorFactory(store.dispatch, selectorFactoryOptions)
    }

    // *************************************
    // 到connect方法中追溯 `selectorFactory` 
    // *************************************

    // options 是在connect方法中第四个参数中初始的值，为true，
     // 主要看 mergeProps  他追溯到connect方法中 initMergeProps 
    // 在追随到 mergeProps 文件，返回的是一个将connect的两个参数合并到props的方法
    // selectorFactory() 执行的时候才会去执行合并的方法，返回合并后的对象
    // 从而挂载到组件上，组件通过props便能访问到redux中管理的值，也能访问到挂载的action
    const selectorFactory = options.pure
    ? pureFinalPropsSelectorFactory
    : impureFinalPropsSelectorFactory

    return selectorFactory(
      mapStateToProps,
      mapDispatchToProps,
      mergeProps,
      dispatch,
      options
    )

  ```

  `mapDispatchToProps` 将action注册成action，实际上是redux 中的bindActionCreator方法代理了一层
  ```javascript
    function bindActionCreator<A extends AnyAction = AnyAction>(
      actionCreator: ActionCreator<A>,
      dispatch: Dispatch
    ) {
      return function (this: any, ...args: any[]) {
        return dispatch(actionCreator.apply(this, args))
      }
    }
  ```
    

## 总结
  
    

