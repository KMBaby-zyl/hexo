title: react学习
date: 2016-04-28 17:55:23
tags: react
---

##1. 背景
> react是Facebook 和 Instagram 用来创建用户界面的 JavaScript 库
>
> 2013 到现在3年多github上有4w个star
>
> React 是为了解决一个问题：构建随着时间数据不断变化的大规模应用程序。只做MVC中的V
>
> 稍微大点的项目都会组件自己的UI库，页面UI的复杂度和交互容易遇到很多麻烦。只解决UI层的问题，兼容性好。UI相关框架 bootstrap,avalon,kissy,jqueryUI...
>
> VDOM性能上可以期待，react 有一套VDOM的渲染算法，官网上有相关英文论文
>
> react来写UI有种web components的感觉。最后出来的UI组件就是一个

	<MyReactUI props={props}/>

##2. react配置环境

1. 包依赖

	```
	"react": "*",
	"react-dom": "^15.0.1",
	"babel-core": "^6.7.6",
	"babel-loader": "^6.2.4",
	"babel-preset-es2015": "^6.6.0",
	"babel-preset-react": "^6.5.0",
	```
2. JSX支持

	sublime: babel-sublime


	打开菜单view， Syntax -> Open all with current extension as... -> Babel -> JavaScript (Babel)，选择babel为默认 javascript 打开syntax

3. chrome 调试工具

	[http://facebook.github.io/react/blog/2015/09/02/new-react-developer-tools.html](http://facebook.github.io/react/blog/2015/09/02/new-react-developer-tools.html)

##3. react入门

	React.createElement() //一个静态HTML
	React.createClass() // react组件
	React.render() //入口函数，可以用来渲染前两个

###组件
	props,state // this.props 表示那些一旦定义，就不再改变的特性，而 this.state 是会随着用户互动而产生变化的特性。

	setState() // 改变state
	setProps() //
	forceUpdate()	// 无法通过改变props，state来render的时候，手动调用它render
	getDOMNode() //获取原生DOM
	propTypes() // 验证
	mixins //
	statics //
	displayName //
	render() //必须,渲染一个React.createElement

### 组件的生命周期

	getInitialState()
	getDefaultProps()
	componentWillMount()
	componentDidMount()
	componentWillReceiveProps()  //在组件接收到新的 props 的时候调用。在初始化渲染的时候，该方法不会调用。
	shouldComponentUpdate() // 在接收到新的 props 或者 state，将要渲染之前调用。该方法在初始化渲染的时候不会调用，在使用 forceUpdate 方法的时候也不会.

	shouldComponentUpdate: function(nextProps, nextState) {
	  return nextProps.id !== this.props.id;
	}
	---------------------------------
	componentWillUpdate() // 在接收到新的 props 或者 state 之前立刻调用。在初始化渲染的时候该方法不会被调用。
	注意：
	你不能在刚方法中使用 this.setState()。如果需要更新 state 来响应某个 prop 的改变，请使用 componentWillReceiveProps。
	componentDidUpdate() // 结束 render()
	componentWillUnmount() // 移除组件

### 事件
>举个栗子click -> onClick 冒泡, onClickCapture 捕获.
>
>也有 stopPropagation() 和 preventDefault()
>
>touch事件开启 React.initializeTouchEvents(true)


[http://reactjs.cn/react/docs/events.html](http://reactjs.cn/react/docs/events.html)

### DOM属性
>支持data-* 和 aria-*
>
>class -> className
>
>  for -> htmlFor

[http://reactjs.cn/react/docs/tags-and-attributes.html](http://reactjs.cn/react/docs/tags-and-attributes.html)
### 自身属性
	key： 可以优化渲染速度，给dom加了个ID
	ref： // 方便调用DOM
	dangerouslySetInnerHTML： // 渲染 html字符串
##4. 数据传递
>单项数据流 从根component到子component
>
>父－子 直接pass props，
>
>子－父，调用父组件传给子的回调 改变父的state或者props
>
>组件间 配置全局的事件.($.jps)

##5. 注意事项



react中文文档：[http://reactjs.cn/react/docs/getting-started.html](http://reactjs.cn/react/docs/getting-started.html)

react英文文档：[https://facebook.github.io/react/](https://facebook.github.io/react/)

react中文社区 [http://react-china.org/](http://react-china.org/)





