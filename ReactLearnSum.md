# React 小结

> 暗号：该吃药了

## 基本使用

​	全局安装`create-react-app`后通过 `create-react-app appName`创建一个react应用

- ### 类组件

  ```js
  export default class App extends React.Component {
     // // 构造函数 可用于继承父组件 可不写
     // constructor(props){
     //     super(props);
     //     // 与定义state={..}类似
     //     this.state = {data:[...]};
     // }
      
      // 类组件中定义储存当前组件的数据状态等
      // 通过this.state获取
      // 不能直接修改，要通过this.setState({data:newdata}),将新数据合并到state上，setState是浅合并
      state={
          data:[...],
      }
                
     	// 可定义多个函数操作数据
      fn1(){
          ...
      }
  
  	// 常用生命周期
  挂载
  	//constructor // 构造函数
  	static getDerivedStateFromProps(props) //将 props 关联到 state 中
  	//render // 虚拟Dom->真实Dom
  	componentDidMount(){} // 在组件已经被渲染到 DOM 中后运行
  
  更新
  	static getDerivedStateFromProps(props) //将新props关联到state中
  	shouldComponentUpdate(newProps,newState){
      // 判断是否需要进行组件更新,newProps,newState新的props和state,
  	// render即根据该两生成虚拟Dom
           return true;// 为falses时中止组件个更新
       }
  	getSnapshotBeforeUpdate //将生成真实Dom的虚拟Dom的快照
      componentDidUpdate //组件更新完成
      
  
      // 渲染函数，每次依赖的数据即state
     	render(){
         
          // render函数返回该组件的Dom结构，必须由一个html包裹，react提供了<Fragment>标签，不会影响其中的内容
          return <Fragment>//JSX
          	...//子组件
          	<children data={this.state.data} /> // 子组件传参，可以传多个参数，在子组件中通过this.prop获取传递过来的数据
          </Fragment>
      }
  }
  ```

  

- ### 函数组件

  - 是一个纯函数，没有this、生命周期、state
  - 组件中的绝大多数功能特性都是通过React Hooks或自定义Hooks来进行
  - Hooks必须在函数的顶部使用
  - 函数式组件更新时，会对其依赖的数据即state和props进行<u>浅对比</u>，如果数据没有修改则不更新
  - 如果数据是引用类型，则更新时要返回新的引用，否则更新不会进行

  ```js
  export default function App(props){
  	
      // 直接返回一个
      return <Fragment>//JSX
          	...//子组件
          </Fragment>
  } 
  ```

  - 常用的Hooks

    - useState()

      - `let [data,setData] = userState(initData)`
      - data 变量名
      - setData() 修改data的方法
      - initDate 初始值

    - useEffect()

      - 默认组件挂载完成、每次更新时执行

        ```js
          useEffect(()=>{
              console.log("组件挂载、更新完成");
          });
        ```

      - 第二个参数传入组件依赖数据，当挂载、数据变动时才执行useEffert

        ```js
        useEffect(()=>{
              console.log("data更新了");
          },[data]);
        ```

        传入空数组，则只在组件挂载和卸载（return)时调用

        ```js
        useEffect(()=>{
              console.log("组件挂载完成");
              return()=>{
              	console.log("组件即将卸载")；
              }
          },[]);
        ```

    - useRef()
      - `let refname = useRef(initValue) `
      - initValue为null，refname作为属性添加的Dom节点上可以关联Dom节点，保存组件更新前的一些状态
      - initValue为某个数据时，可以保留该数据更新前的内容

  - 自定义Hooks
    - 类似单独封装一个功能函数，但是可以在其中配合使用React Hooks
    - 函数命名必须以 use 开头

- ### React-router

  - 基本用法

    - ```js
      <Route 
          path={"/"} // url路径
          exact	// 精确匹配
          render={(routeProps)=>{ // 路由参数
              return <IndexPage // 显示的组件
              userName={userName} // 组件传参
              {...routeProps}	// 将路由参数传递给组件
              />
          }}
      />
      ```

    - nav组件中

      ```js
      <Link to="/">首页</Link>
      <Link to="/about">关于我们</Link>
      <Link to="/join">加入我们</Link>
      ```

      - NavLink

        activeClassName、activeStyle当url匹配对应时，则生效

        isActive() 接受一个函数，判断是否选中，返回boolean，true则选中生效

        ```js
        	<NavLink 
                to="/"
                exact
                activeClassName="selected"
                activeStyle={{
                  fontWeight: "bold"
                }}
                isActive = {()=>{
                    return true
                }}
              >首页</NavLink>
        ```

  - 路由模糊匹配

    - ```js
      <Route 
          path={"/about"}
          exact
          render={(routeProps)=>{
              return <AboutPage />
          }}
      />
      <Route 
         path={["/","/:type","/:type/:page"]}
         exact
         render={(routeProps)=>{
             ...//
         }
       />
      ```

    - 此时`/:type`也会匹配`/about`路径，所以应将`/about`置于`/:type`之上避免路由无效

  - 路由重定向Redirect

    - ```js
      <Route 
          path="/"
          exact
          render={()=>{
          return <Redirect to="/index" /> // 重定向当前路径
      	}}
      />
      ```

  - 404

    - 当path无效时，就会匹配成功

      ```js
      <Route 
      	component={Page404}
      />
      ```

- ### Redux    react-redux

  - 适配React的一个状态管理库
  - 三大原则
    - 单一数据源,应用的数据state都被贮存在一个object中，只存在唯一一个store
    - state时只读的
    - state只能通过触发action调用reducers来改变

  - action

    - 一个对象，包含了type指明要进行的动作，以及可能需要传入的参数，type是必要

      ```js
      {
        type: ADD_TODO,
        // ... 
      }
      ```

  - reducer

    - 通过`dispatch()`触发action后，action发送到reducers，reducers便根据action的type（可能有另外的参数等），返回一个新的state，进行state的更新

    - 是纯函数

      ```js
      const reducer = (state = { data: []，...}, action) => {
          switch (action.type) {
              case 'ADD':
                  return {
                      ...state,
                      data: [ action.data]
                  }
              default:
                  return state;
          }
      }
      ```

  - store

    - 根据已有的reducers创建一个对外提供方法的store，即action、reducers是在构建仓库内部结构，store是结合该俩为一个真正的仓库，对外提供方法来使用它
    - 提供 `getState()` 方法获取 state；
    - 提供 `dispatch(action)` 方法更新 state；
    - 通过 `subscribe(listener)` 注册监听器;
    - 通过 `subscribe(listener)` 返回的函数注销监听器。

