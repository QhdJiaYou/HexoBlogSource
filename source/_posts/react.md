---
title: 初识React
date: 2020-09-01 17:50:43
---

## React生命周期函数



### **挂载阶段只执行一次**

`constructor()`一般用来做初始化工作，例如初始化state

`componentWillMount()`   已更名为  UNSAFE_componentWillMount()  如果在此函数中使用setState()并不会触发渲染，因为render()还没有执行，所以一般初始化state使用constructor()函数。

`render()`

`componentDidMount()`一般用于请求异步加载的数据，添加事件监听



### 更新阶段可多次执行，当props或state发生变化时

`componentWillReceiveProps(object:nextProps)` 已更名为  UNSAFE_componentWillReceiveProps()       已加载组件收到新的props时调用：如果父组件导致该组件重新渲染，这个方法也会被调用，即使props没有任何改变，如果您只想处理更改，请务必比较当前值和下一个值，你可以比较this.props和nextProps，并在这个方法中使用this.setState()执行状态转换。

`shouldComponentUpdate(object:nextProps, object:nextState)`组件判断是否重新渲染时调用

`componentWillUpdate(object:nextProps, object:nextState)`已更名为 UNSAFE_componentWillUpdate() 组件被重新渲染之前: 不要在这个函数中使用setState()

`render()`

`componentDidUpdate(object:prevProps, object:prevState)`处理在生命周期中由于state或者props的变化触发新的请求

`componentWillUnmount()`已经移除真实DOM时



**React旧版本生命周期图**：

![avatar](https://upload-images.jianshu.io/upload_images/5287253-315eac1c26082f08.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)


**React新版本生命周期图**：

![avatar](https://upload-images.jianshu.io/upload_images/5287253-19b835e6e7802233.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)


### 变更的部分

新的react生命周期去掉了以下三个：

- componentWillMount
- componentWillReceiveProps
- componentWillUpdate

同时为了弥补失去上面三个函数的不足又加了两个：

- static getDerivedStateFromProps
- getSnapshotBeforeUpdate

当然，这个更替是缓慢的，在整个16版本里都能无障碍的使用旧的三生命周期，但值得注意的是，旧的生命周期（unsafe）不能和新的生命周期同时出现在一个组件，否则会报错“你使用了一个不安全的生命周期”。

### 为什么要改

旧的生命周期十分完整，基本可以捕捉到组件更新的每一个state/props/ref，没有什么从逻辑上的毛病。

但是架不住官方自己搞事情，react打算在17版本推出新的Async Rendering（也就是Fiber架构），提出一种可被打断的生命周期，而**可以被打断的阶段正是实际dom挂载之前的虚拟dom构建阶段**，也就是要被去掉的三个生命周期。

生命周期一旦被打断，下次恢复的时候又会再跑一次之前的生命周期，因此componentWillMount，componentWillReceiveProps， componentWillUpdate都不能保证只在挂载/拿到props/状态变化的时候刷新一次了，所以这三个方法被标记为不安全。

### 两个新的生命周期

- **static getDerivedStateFromProps(nextProps, prevState)**

**触发时间**：组件每次被渲染时（包括组件构建之后），每次获取新的props或state之后。

**返回值**：每次接收新的props之后都会返回一个对象作为新的state,返回null则说明不需要更新state

**作用**：配合componentDidUpdate,可以覆盖componentWillReceiveProps的所有用法

- **getSnapshotBeforeUpdate(prevProps, prevState)**

**触发时间**: update发生的时候，在render之后，在组件dom渲染挂载之前。

**返回值**：作为componentDidUpdate(prevProps, prevState, snapshot)的第三个参数。

**作用**：配合componentDidUpdate, 可以覆盖componentWillUpdate的所有用法。

## React组件区别

1、组件的名称必须以大写字母开头

2、组件的返回值只能有一个根元素

3、函数组件

```react
function Welcome (props) {
  return <h1>Welcome { props.name }</h1>
}
ReactDOM.render(<Welcome name='react' />, document.getElementById('root'));
```

4、类组件

```react
class Welcome extends React.Component(props) {
  render() {
  	return (
      <h1>Welcome { this.props.name }</h1>
    );
  }
}
ReactDOM.render(<Welcome name='react' />, document.getElementById('root'));
```

5、所有react组件必须都是**纯函数**(函数内部不会修改输入参数，输入相同，输出必然相同)，无论是哪种组件都不能修改它自己的props

6、组件的属性和状态改变都会更新视图

7、函数组件和类组件的区别

| 区别            | 函数组件 | 类组件 |
| --------------- | -------- | ------ |
| 是否有this      | 没有     | 有     |
| 是否有生命周期  | 没有     | 有     |
| 是否有状态state | 没有     | 有     |

函数组件的性能要比类组件的性能高，因为类组件使用的时候要实例化，函数组件直接执行函数取返回结果即可。

## render()何时被触发

1. 一个组件必须通过setState()去改变this.state的状态，才能引发重新渲染，或者是组件的props状态改变也可引发重新渲染；
3. 父组件重新渲染也会导致子组件的重新渲染，有时候为了避免不必要的渲染，我们需要在shouldComponentUpdate()中将新老state和props做对比，如果没有发生改变就返回false，可以阻止component渲染；
3. 当然有时候这种简单的判断，显得有些多余和样板化，于是react就提供了PureComponent来自动对比；
4. **但是PureComponent自动添加的shouldComponentUpate函数，只是对props和state进行浅比较(shadow comparison)，当props或者state本身是嵌套对象或数组时，浅比较并不能得到预期的结果，这很可能会导致实际的props和state发生了变化，*但组件却没有更新的问题***。
5. 浅比较都做了什么？

```react
if (this._compositeType === CompositeTypes.PureClass) {
shouldUpdate = !shallowEqual(prevProps, nextProps)
|| !shallowEqual(inst.state, nextState);
}
```

- 比较 `Object.keys(state | props)` 的长度是否一致
- 每一个 `key`是否两者都有
- 相同的`key`是否是同一个引用

## **state**更新

1. 异步更新：由 **React** 控制的事件处理过程 **setState** 不会同步更新 **this.state**！
   - 在大部分的使用情况下，我们都是使用了 React 库中的表单组件，例如 select、input、button 等等，它们都是 React 库中人造的组件与事件，是处于 React 库的控制之下，比如组件原色 onClick 都是经过 React 包装。在这个情况下，setState 就会以异步的方式执行。也就意味着大部分函数中都会异步更新。
2. 同步更新：在 **React** 控制之外的情况， **setState** 会同步更新 **this.state**！
   - 通过addEventListener直接添加的事件处理函数，或者setTimeout/setInterval产生的异步调用可以同步更新。

## 使用**setState**注意

1. 不要直接修改state，通过setState()方法修改才能触发重复渲染
2. setState()的参数不是一个对象，而是一个函数时，这个函数默认两个参数分别是state和props，这个函数应该返回一个对象表示对state的更改
3. 每次setState产生新的state会依次被存入一个队列，然后会根据isBathingUpdates变量判断是直接更新this.state，还是放进dirtyComponent里回头再说，isBathingUpdates默认是false, 也就表示setState会同步更新this.state。但是，当React在调用事件处理函数之前就会调batchedUpdates，这个函数会把isBatchingUpdates修改为true，造成的后果就是由React控制的事件处理过程setState不会同步更新this.state。
4. 函数用到更新的state变量时，要将函数写到setState()函数的第二个参数（接受一个回调函数）
5. 事实上，setState 方法与包含在其中的执行是一个很复杂的过程，从 React 最初的版本到现在，也有无数次的修改。它的工作除了要更动 this.state 之外，还要负责触发重新渲染，这里面要经过 React 核心 diff 算法，最终才能决定是否要进行重渲染，以及如何渲染。而且为了批次与效能的理由，多个 setState 呼叫有可能在执行过程中还需要被合并，所以它被设计以延时的来进行执行是相当合理的。 
6. React官方建议把state当作是不可变对象，state中包含的所有状态都应该是不可变的对象，当state中的某个状态发生变化时，我们应该重新创建这个状态对象，而不是直接修改原来的状态。

​       **值类型变量**：number,boolean,string,null,undefined本身就是不可变的，修改时直接赋值就可以了

```react
this.setState({
   num: 1,
   string: 'hello',
   ready: true
 });
```

​	**数组类型变量**：若要从原来的数组中获取部分作为新的state值，应使用slice、concat、filter这种不会修改		    原数组的函数，不应该使用push、pop、 shift、 unshift、 splice在原来的数组上进行操作。

```react
let students = this.state.students;
   this.setState({
     students: students.concat(['xiaohua'])
   });

   //或者ES6语法
   this.setState(preState => ({
     students: [ ...preState.students, 'xiaohua']
   });
```

​	**普通对象**：对象也是可变类型，修改对象类型的状态时，应该保证不会修改原来的状态，可以使用ES6的Object.assign()方法，或者对象扩展的语法

```react
//Object.assign方法
this.setState(preState => ({
  school: Object.assign({}, preState.school, {classNum: 10})
}));

//对象扩展语法
let school = this.state.school;
this.setState({
  school: { ...school, classNum: 10 }
})
```



## PureComponent

React15.3中新增的类，在普通的component中，我们为了减少不必要的渲染render，提高性能，会在shouldComponentUpdate()中判断组件的props和state是否发生了改变，如果没有变化就返回false不会触发render方法，省去了Virtual DOM 的生成和对比过程。

## React路由

目前官网同时维护2.x(react-router)和4.x(react-router-dom)版本的路由，两个版本用法不同，不能兼容，现在我们使用npm安装react-router基本都是4.x版本的。

### 属性exact和strict

我们在使用route的时候匹配路径的规则是模糊的，类似正则匹配的贪婪匹配，为了避免如下图所示匹配失效的情况，我们应该正确使用exact和strict属性。

```react
ReactDom.render(
    <HashRouter>
        <Switch>
            <Route path = '/' component={App} />
            <Route path = '/PageA' component={PageA} />
            <Route path = '/PageB' component={PageB} />
        </Switch>
    </HashRouter>, document.getElementById('root')
 );
```

这种情况下访问http://localhost:3000/#/和http://localhost:3000/#/PageA都会渲染App组件，即使hash值已经改变。

**exact属性**：默认不写为false，当它为true时，路径中的hash值必须和path完全一致才渲染对应的组件（如果strict属性为false，则末尾是否包含反斜杠结尾不影响匹配结果）

**strict属性**：默认不写为false，如果strict属性为true，并且path中不包含反斜杠结尾，那么它也不能匹配包含反斜杠结尾的路径。(一般配合exact同时使用)

两个属性同时使用是最精确的匹配

| exact | strict | path | Location.pathname | Matches? |
| :---: | ------ | ---- | ----------------- | -------- |
| True  | False  | /one | /one/             | True     |
| True  | True   | /one | /one/             | False    |

**注意**：通过hash路由进行页面跳转导航时，只会加载相应的组件内容，运行组件的生命周期函数，不会运行模块的代码，也就是组件之外的属于模块级别的代码，这些属于模块的代码只在第一次加载该页面时运行。

失败场景：组件中某个样式的展示是根据声明在模块中的变量来决定，此时通过path的改变跳转到其他组件，然后再通过路由跳转回原来的页面，由于第二次跳转到这个页面时不会在运行模块级别的代码，就可能会导致组件中某些样式的展示与第一次不同。



**react-router-dom使用注意：**

如果想在某个路由下新建子路由，那么这个路由匹配时不能使用exact，否则会找不到页面

<Route/>放哪组件就显示在哪

使用<BrowserRouter>需要服务端做配置，将任何请求都转发到index.html中的根路由上

<HashRouter>不需要做额外处理，因为发送到服务器的请求永远都是#前面的内容，都是同一个页面，#后面的值用来做前端页面路由，对浏览器的兼容性更好，支持老版本浏览器。

<Link>里面一般用props.match.url

<Route>里面一般使用props.match.path

`HashRouter` 使用 URL 中的 hash（#）部分去创建形如 `example.com/#/some/path` 的路由，hash值后面的内容都不会发送到服务端，hash后面的内容纯粹是前端页面路由。

`BrowserRouter` 是使用浏览器中的[HTML5的History API](https://www.renfei.org/blog/html5-introduction-3-history-api.html)处理 URL的，每次都会创建一个像example.com/list/123`这样真实的 URL 发送给服务端。

**为什么BrowserRouter需要服务端配置？**

因为真实URL其实是指向服务器资源，当通过真实URL访问网站的时候，第一次访问的是网站的域名，这个时候可以正常加载我们的网站js等文件，而用户手动刷新网页时，由于路径是指向服务器的真实路径，服务器端没有做相应的路由配置，就会导致资源不存在，用户访问的资源不存在，返回给用户的是404错误。
通过hashHistory来生成的URL就不会出现这样的问题，因为他不是指向真实的路由。

**服务端配置原理？**

其本质的原理就是利用服务端将任何请求都指向`index.html`，而在React应用中index.html又刚好通过React-Router配置了相应的路由，我们让服务器返回index.html，后面就交给前端路由来实现无刷新加载对应页面。

## React之ref

在典型的React数据流中，props是唯一的父组件与子组件间的通讯方式，通过给子组件传递不同的参数，也可以达到组件复用的优势。但是在某些情况下，被更改的元素可能是一个react组件的实例，或者是一个DOM元素，这个时候就要用ref。

#### 创建

一般在构造函数中React.createRef()创建的ref，当组件被创建时分配给实例变量，这样render中的DOM节点就能够被引用，但是不要过度使用ref

```react
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />
  }
}
```

#### **访问**

当一个ref通过render放入一个元素中，要想在其它函数中获得该元素对象的引用，可以通过ref的current属性得到。

```react
const node = this.myRef.current;
```

#### **ref的值根据节点类型的不同而不同**

1. 当ref属性用于html元素，在构造器中通过React.createRef()函数创建的ref，接收底层DOM元素作为它的current属性
2. 当ref属性用于传统的类组件，ref对象接收挂载好的组件实例作为它的current属性
3. ref属性不能用于函数式组件上，因为他们并没有实例，但是我们可以在函数式组件中使用ref属性。

#### ref使用注意

一般什么时候使用？

- 管理焦点、文本选择、媒体回放
- 触发必要动画；
- 整合第三方DOM库

避免对任何可以声明式解决的问题使用Refs；比如相对于暴露一个对话框组件的open()、close()方法，请使用isOpen prop！不要过度使用！

#### 向父组件暴露子组件的Dom元素的引用使用[ref forwarding](https://reactjs.org/docs/forwarding-refs.html)

在很罕见的情况下，我们需要从父组件访问到子组件的DOM节点。通常来说我们不建议这样做，因为这样破坏了组件的封装性，但是在某些情况下对于类似：触发聚焦、改变子元素DOM节点的大小、位置等情况非常有用。

```react
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

**注意**：传统方式定义的类组件以及函数组件是不能够接收参数ref的，只有通过React.forwardRef()函数创建的组件才可以接受ref参数。

#### 回调ref

React将会在组件挂载时使用DOM元素调用ref回调，在组件卸载时使用null调用ref回调。ref回调都会在componentDidMount或者componentDidUpdate生命周期钩子之前被调用。

```react
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = null;
    this.setTextInputRef = element => {
        this.textInput = element;
    };
    this.focusTextInput = () => {
      // 通过原生DOM API聚焦文本
      if (this.textInput) this.textInput.focus();
    };
  }
  
  componentDidMount() {
    // 在挂载时自动聚焦
    this.focusTextInput();
  }

  render() {
    // 使用'ref'回调去在一个实例域中储存文本输入DOM元素的引用(比如, this.textInput).
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

