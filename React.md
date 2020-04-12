## 核心

### 为什么要用React

解决了三个问题： 1.组件复用问题  2.性能问题  3.兼容性问题

优点：

- 只需查看 render 函数就会很容易知道一个组件是如何被渲染的

- JSX 的引入，使得组件的代码更加可读，也更容易看懂组件的布局，或者组件之间是如何互相引用的

- 支持服务端渲染，这可以改进 SEO 和性能

- 易于测试

- React 只关注 View 层，所以可以和其它任何框架(如Backbone.js, Angular.js)一起使用

## JSX

### 为什么要用JSX

- JSX 执行更快，因为它在编译为 JavaScript 代码后进行了优化。
- 它是类型安全的，在编译过程中就能发现错误。
- 使用 JSX 编写模板更加简单快速。

## 元素渲染

### 元素是构成 React 应用的最小砖块

### 将一个元素渲染为DOM

想要将一个React元素渲染到根DOM节点中，只需把它们一起传入`ReactDOM.render()`

```js
const element = <h1>Hello, world</h1>;
reactDOM.render(element, document.getElementById('root'));
```

### 更新已渲染的元素

React元素是**不可变对象**。一旦被创建，就无法改变它的子元素或者属性。一个元素就像电影的单帧，代表了某个特定时刻的UI

更新UI唯一的方式就是创建一个全新的元素，并将其传入`ReactDOM.render()`

***React 只更新它需要更新的部分***

## 组件 & Props

### 函数组件与类组件

- 函数组件：使用`javascript`函数定义组件

```js
function Welcome(props){
    return <h1>Hello, {props.name}</h1>;
}
```

该函数是一个有效的`React`组件，因为它接收唯一带有数据的"props"（代表属性）对象并返回一个`React`元素。这类组件被称为"函数组件"，因为它本质就是`JavaScript`函数

- 类组件：使用`ES6`的`class`来定义组件

```js
class Welcome extends React.Component {
    render(){
        return <h1>Hello, {this.props.name}</h1>;
    }
}
```

- React.createClass -- 已废弃，不推荐使用

```js
var Welcome = React.createClass({
    render: function(){
        return <h1>Hello, {this.props.name}</h1>;
    }
})
```

### 渲染组件

`React`元素可以是DOM标签

```js
const element = <div />
```

`React`元素也可以是用户自定义的组件，它会将JSX所接收的**属性**（attributes）以及**子组件**（children）转换为单个对象传递给组件，这个对象称之为"props"

```js
const element = <Welcome name="Sara" />
```

下面代码会在页面上渲染"Hello Sara"

```js
function Welcome(props){
    return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
	element,
    document.getElementById('root')
);
```

以上代码逻辑

1. 调用`ReactDOM.render()`函数，并传入`<Welcome name="Sara" />`作为参数
2. React调用`Welcome`组件，并将`{name: 'Sara'}`作为props传入
3. `Welcome`组件将`<h1>Hello, Sara</h1>`元素作为返回值
4. React DOM将DOM高效地更新为`<h1>Hello, Sara</h1>`

***组件的名称必须以大写字母开头，除此之外还需要注意组件类只能包含一个顶层标签，否则也会报错***

React会将以小写字母开头的组件视为原生DOM标签。例如，`<div />`代表HTML的div标签，而`<Welcome />`则代表一个组件，并且需要在作用域内使用组件

### 组合组件

组件可以在其输出中引用其他组件。这就可以让我们用同一组件来抽象出任意层次的细节。按钮、表单、对话框、甚至整个屏幕的内容：在React程序中，这些都会以组件的形式表示

### 提取组件

以下代码是`Comment`组件，用于描述一个社交媒体网站上的评论功能，它接收`author`（对象），`text`（字符串）以及`date`（日期）作为props

```js
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

该组件由于嵌套的关系变得难以维护，且很难复用它的各个部分。因此可以从中提取一些组件出来

1. 提取`Avatar`组件

```js
function Avatar(props){
    return (
    	<img className="Avatar"
          src={props.user.avatartUrl}
		  alt={props.user.name}
		/>
    );
}
```

此时的`Comment`组件可以简化为

```js
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

2. 接下来提取`UserInfo`组件，该组件在用户名旁渲染`Avatar`组件

```js
function UserInfo(props){
    return (
    	<div className="UserInfo">
        	<Avatar user={props.user} />
			<div className="UserInfo-name">
                {props.user.name}
			</div>
		</div>
    );
}
```

此时的`Comment`组件可以简化为

```js
function Comment(props){
    return (
    	<div className="Comment">
        	<UserInfo user={props.author} />
			<div className="Comment-text">
                {props.text}
			</div>
			<div className="Comment-date">
                {formatDate(props.date)}
            </div>
        </div>
    );
}
```

如果UI中有一部分被多次使用（`button`，`Panel`，`Avatar`），或者组件本身就足够复杂（`App`，`FeedStory`，`Comment`），那么它就是一个可复用组件的候选项

### Props的可读性

组件无论是使用***函数声明还是通过class声明***，都绝不能修改自身的props

***所有的React组件都必须像纯函数一样保护它们的props不被修改***

**纯函数**：不会尝试更改入参，且多次调用下相同的入参始终返回相同的结果

## state

前一节时钟的例子忽略了一个技术细节：`Clock`组件需要设置一个计时器，并且需要每秒更新UI

理想情况下希望只写一次代码，便可以让`Clock`组件自我更新

```js
ReactDOM.render(
	<Clock />,
    document.getElementById('root')
);
```

需要在`Clock`组件中添加"state"来实现这个功能

"state"与"props"类似，但是"state"是私有的，并且完全受控于当前组件

以下将`props`转换为`state`

### 1. 将函数组件转换成class组件

通过以下五步将`Clock`的函数组件转换成函数组件

1. 创建一个同名的`ES6 class`，并且继承于`React.Component`
2. 添加一个空的`render()`方法
3. 将函数题移动到`render()`方法之中
4. 在`render()`方法中使用`this.props`代替`props`
5. 删除剩余的空函数声明

```js
class Clock extends React.Component{
    render(){
        return (
        	<div>
            	<h1>Hello, World</h1>
            	<h2>It is {this.props.date.toLocaleTimeString()}</h2>
            </div>
        )
    }
}
```

### 2. 向class组件中添加局部的state

通过以下三步将`date`从props移动到state中

1. 把`render()`方法中的`this.props.date`替换成`this.state.date`

```js
class Clock extends React.Component{
    render(){
        return (
        	<div>
            	<h1>Hello, World</h1>
            	<h2>It is {this.state.date.toLocaleTimeString()}</h2>
            </div>
        );
    }
}
```

2. 添加一个`class构造函数`，然后再该函数中为`this.state`赋初值

```js
class Clock extends React.Component{
    constructor(props) {
        super(props);
        this.state = {date: new Date()};
    }
    
    render(){
        return (
        	<div>
            	<h1>Hello, World</h1>
            	<h2>It is {this.state.date.toLocaleTimeString()}</h2>
            </div>
        )
    }
}
```

通过以下方式将`props`传递到父类的构造函数中

```js
constructor(props){
    super(props);
    this.state = {date: new Date()};
}
```

Class组件应该始终使用`props`参数来调用父类的构造函数

3. 移除<Clock />元素中的`date`属性

```js
ReactDOM.render(<Clock />, document.getElementById('root'));
```

### 3. 将生命周期方法添加到Class中

在具有许多组件的应用程序中，在销毁时释放组件所占用的资源非常重要。

每当 Clock 组件第一次加载到 DOM 中的时候，我们都想生成定时器，这在 React 中被称为**挂载**。

同样，每当 Clock 生成的这个 DOM 被移除的时候，我们也会想要清除定时器，这在 React 中被称为**卸载**。

我们可以在组件类上声明特殊的方法，当组件挂载或卸载时，来运行一些代码：

```js
class Clock extends React.Component{
    constructor(props){
        super(props);
        this.state = {date: new Date()};
    }
    
    // 挂载
    componentDidMount(){
        // this.props 和 this.state 是 React 本身设置的，拥有特殊的含义，但是可以向 class 中随意添加不参与数据流（比如计时器 ID）的额外字段。
        this.timerID = setInterval(
        	() => this.tick(),
   			1000
        )
    }
    
    // 卸载
    componentWillUnMount(){
        clearInterval(this.timerID);
    }
    
    tick(){
        this.setState({
            date: new Date();
        })
    }
    
    render(){
        return (
        	<div>
            	<h1>Hello, World</h1>
            	<h2>It id {this.state.date.toLocaleTimeString()}</h2>
            </div>
        );
    }
}
```

`componentDidMount()`与 `componentWillUnmount()` 方法被称作生命周期钩子。

在组件输出到 DOM 后会执行 `componentDidMount()` 钩子，我们就可以在这个钩子上设置一个定时器。

`this.timerID` 为定时器的 ID，我们可以在 `componentWillUnmount()` 钩子中卸载定时器。

**代码执行顺序**

1. 当`<Clock />`被传递给`ReactDOM.render()`时，React 调用 Clock 组件的构造函数。由于 Clock 需要显示当前时间，所以使用包含当前时间的对象来初始化`this.state`。稍后会更新此状态
2. 然后 React 调用 Clock 组件的`render()`方法。这是 React 了解屏幕上应该显示什么内容，然后 React 更新 DOM 以匹配 Clock 的渲染输出
3. 当 Clock 的输出插入到 DOM 中时，React 调用`componentDidMount()`生命周期钩子。在其中，Clock 组件要求浏览器设置一个定时器，每秒钟调用一次`tick()`
4. 浏览器每秒钟调用`tick()`方法。其中 Clock 组件通过包含当前时间的对象调用`setState()`来调度 UI 更新。通过调用`setState()`，React 知道状态已经改变，并在此调用`render()`方法来确定屏幕上应当显示什么。这一次，`render()`方法中的`this.state.date`将不同，所以渲染输出将包含更新的时间，并相应的更新DOM
5. 一旦 Clock 组件从 DOM 中移除，React 会调用`componentWillUnmount()`这个钩子函数，定时器会被清除

### 正确地使用 State

1. 不要直接修改State，应该使用`setState()`来修改state

```js
// Wrong
this.state.comment = 'hello';

// Correct
this.setState({comment: 'hello'});
```

2. state 的更新可能是异步的

出于性能考虑，React 可能会把多个`setState()`调用合并成一个调用。因为`this.props`和`this.state`可能会异步更新，所以不能依赖它们的值来更新下一个状态

解决这个问题，可以让`setState()`接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做第二个参数

```js
// Wrong
this.setState({
    counter: this.state.counter + this.props.increment
});

// Correct 也可以不用箭头函数，使用普通函数也同样可以
this.setState((state, props) => ({
    counter: state.counter + props.increment
}))
```

3. state 的更新会被合并

当调用`setState()`的时候，React 会将提供的对象合并到当前的 state

```js
// 例如，state 包含几个独立变量
constructor(props){
    super(props);
    this.state = {
        posts: [],
        comments: []
    }
}

// 分别用`setState()`来更新
componentDidMount(){
    fetchPosts().then(response => {
        this.setState({
            posts: response.posts
        });
    });
    fetchComments().then(response => {
        this.setState({
            comments: response.comments
        })
    })
}
```

这里的合并是浅合并，所以`this.setState({comments})`完整保留了`this.state.posts`，但是完全替换了`this.state.comments`

### 数据自顶向下流动

父组件或子组件都不能知道某个组件是有状态还是无状态，并且它们不应该关心某组件是被定义为一个函数还是一个类

这就是为什么状态通常被称为局部或封装。 除了拥有并设它的组件外，其它组件不可访问

以下实例中 FormattedDate 组件将在其属性中接收到 date 值，并且不知道它是来自 Clock 状态、还是来自 Clock 的属性、亦或手工输入

```js
function FormattedDate(props) {
  return <h2>现在是 {props.date.toLocaleTimeString()}.</h2>;
}
 
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
 
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
 
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
 
  tick() {
    this.setState({
      date: new Date()
    });
  }
 
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <FormattedDate date={this.state.date} />
      </div>
    );
  }
}
```

这通常被称为自顶向下或单向数据流。 任何状态始终由某些特定组件所有，并且从该状态导出的任何数据或 UI 只能影响树中下方的组件

如果你想象一个组件树作为属性的瀑布，每个组件的状态就像一个额外的水源，它连接在一个任意点，但也流下来

为了表明所有组件都是真正隔离的，我们可以创建一个 App 组件，它渲染三个Clock

```js
function FormattedDate(props) {
  return <h2>现在是 {props.date.toLocaleTimeString()}.</h2>;
}
 
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
 
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
 
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
 
  tick() {
    this.setState({
      date: new Date()
    });
  }
 
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <FormattedDate date={this.state.date} />
      </div>
    );
  }
}
 
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}
```

以上实例中每个 Clock 组件都建立了自己的定时器并且独立更新

在 React 应用程序中，组件是有状态还是无状态被认为是可能随时间而变化的组件的实现细节

我们可以在有状态组件中使用无状态组件，也可以在无状态组件中使用有状态组件

## React事件处理

### React事件处理与DOM事件处理

- React 事件绑定属性的命名采用驼峰式写法，而不是小写
- 如果采用 JSX 的语法需要传入一个函数作为事件处理函数，而不是一个字符串
- React 中不能用返回 `false` 的方式阻止默认行为，必须明确使用 `preventDefault`
- 使用 React 的时候不需要使用 `addEventListener` 为一个已创建的 DOM 元素添加监听器，仅仅需要在这个元素开始渲染的时候提供一个监听器

``` 
// DOM 方式
<a href="#" onclick="console.log('点击链接'); return false">
	点我
</a>

// React 方式
function ActionLink(){
	function handleClick(e){
		e.preventDefault();
		console.log('链接被点击');
	}
	
	return (
		<a href="#" onClick={handleClick}>
			点我
		</a>
	)
}
```

### ES6类中的事件

```js
class Toggle extends React.Component{
    constructor(props){
        super(props);
        this.state = {isToggleOn: true};
        // 这边绑定是必要的，这样this才能在回调函数中使用
    	this.handleClick = this.handleClick.bind(this);
    }
    
    handleClick(){
        this.setState(prevState => ({
            isToggleOn: !prevState.isToggleOn;
        }))
    }
    
    render(){
        return (
        	<button onClick={this.handleClick}>
            	{this.state.isToggleOn? 'ON': 'OFF'}
            </button>
        );
    }
}
```

**类的方法不会默认绑定`this`的。如果忘记绑定`this.handleClick`并把它传入`onClick`，当调用这个函数时`this`的值会是`undefined`**如果没有在方法后面添加`()`，就应该为这个方法绑定`this`

还有另外两种办法可以解决`this`的绑定问题

1. 使用属性初始化器

```js
class Toggle extends React.Component{
            constructor(props){
                super(props);
                this.state = {isToggleOn: true};

                // 绑定事件
                //this.handleClick = this.handleClick.bind(this);
            }
			
    		// 要绑定的事件采用箭头函数的方式
            handleClick = () => {
                this.setState(prevState => ({
                    isToggleOn: !prevState.isToggleOn
                }))
            }

            render(){
                return (
                    <button onClick={this.handleClick}>
                        {this.state.isToggleOn? 'ON': 'OFF'}
                    </button>
                );
            }
        }
```

2. 在回调函数中使用箭头函数

```js
class Toggle extends React.Component{
            constructor(props){
                super(props);
                this.state = {isToggleOn: true};

                // 绑定事件
                //this.handleClick = this.handleClick.bind(this);
            }

            handleClick(){
                this.setState(prevState => ({
                    isToggleOn: !prevState.isToggleOn
                }))
            }

            render(){
                return (
                    // 绑定this
                    <button onClick={(e) => this.handleClick(e)}>
                        {this.state.isToggleOn? 'ON': 'OFF'}
                    </button>
                );
            }
        }
```



**使用这个语法的问题是每次Toggle渲染的时候都会创建一个不同的回调函数。在大多数情况下这没有问题。然而如果这个回调函数作为一个属性值传入低阶组件，这些组件可能会进行额外的重新渲染。** ***建议在构造函数中绑定或使用属性初始化器语法来避免这类性能问题***

### 向事件处理程序传递参数

在循环中通常会为事件处理函数传递额外的参数。例如，若`id`是你要删除的那一行的ID，以下两种方式都可以向事件处理函数传递参数：

```js
// 通过箭头函数的方式，事件对象必须显式的进行传递
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
// 通过bind的方式，事件对象以及更多的参数将会被隐式的进行传递
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

**通过`bind`方式向监听函数传参，在类组件中定义的监听函数，事件对象e要排在所传参数的后面**

```js
class Popper extends React.Component{
    constructor(){
        super();
        this.state = {name:'Hello world!'};
    }
    
    preventPop(name, e){    //事件对象e要放在最后
        e.preventDefault();
        alert(name);
    }
    
    render(){
        return (
            <div>
                <p>hello</p>
                {/* 通过 bind() 方法传递参数。 */}
                <a href="https://reactjs.org" onClick={this.preventPop.bind(this,this.state.name)}>Click</a>
            </div>
        );
    }
}
```

## React 条件渲染

### 使用 if 进行条件渲染

使用if来表示创建当前状态的元素，让React根据它们来更新UI

```js
function UserGreeting(props){
    return <h1>欢迎回来！</h1>
}

function GuestGreeting(props) {
    return <h1>请先注册！</h1>
}

function Greeting(props) {
    const isLoggedIn = props.isLoggedIn;
    if(isLoggedIn)
        return <UserGreeting />;
    else
        return <GuestGreeting />
}
```

### 使用 元素变量 进行条件渲染

```js
function LogoutButton(props){
    return (
        // props的onClick属性为函数
        <button onClick={props.onClick}>
            退出登录
        </button>
    );
}

function LoginButton(props){
    return (
        // props的onClick属性为函数
        <button onClick={props.onClick}>
            登录
        </button>
    );
}

class LoginControl extends React.Component{
    constructor(props){
        super(props);
        this.handleLoginClick = this.handleLoginClick.bind(this);
        this.handleLogoutClick = this.handleLogoutClick.bind(this);
        this.state = {isLoggedIn: false};
    }

    handleLoginClick(){
        this.setState({
            isLoggedIn: true
        })
    }

    handleLogoutClick(){
        this.setState({
            isLoggedIn: false
        })
    }

    render(){
        const isLoggedIn = this.state.isLoggedIn;
        let button;

        if(isLoggedIn)
            button = <LogoutButton onClick={this.handleLogoutClick} />;
        else
            button = <LoginButton onClick={this.handleLoginClick} />;

        return (
            <div>
                <Greeting isLoggedIn={isLoggedIn} />
				// JSX {} 真是神奇的东西
                {button}
            </div>
        );
    }
}
```

### 使用 运算符&& 进行条件渲染

 ```js
function Mailbox(props){
    const unreadMessages = props.unreadMessages;
    return (
        <div>
            <h1>Hello!</h1>
            {unreadMessages.length > 0 &&
                <h2>
                    you have {unreadMessages.length} unread messages.
                </h2>
            }
        </div>
    );
}
//const messages =[];
const messages = ['React', 'Re: React', 'Re: Re: React'];
 ```

**在`javascript`中，`true && expression`总是会返回`expression`，而`false && expression`总是会返回`false`**

***因此，如果条件是`true`，`&&`右侧的元素会被渲染，如果是`false`，React会忽略并跳过它***

### 三目运算符

三目运算符`condition? true: false`

```js
render(){
    const isLoggedIn = this.state.isLoggedIn;
    return (
    	<div>
        	The user is <b>{isLoggedIn? 'currently': 'not'}</b> logged in.
        </div>
    );
}
```

### 阻止条件渲染

 在极少数情况下，可能希望能隐藏组件，即使它已经被其他组件渲染。若要完成此操作，可以让 `render` 方法直接返回 `null`，而不进行任何渲染。 

 下面的示例中，`<WarningBanner />` 会根据 prop 中 `warn` 的值来进行条件渲染。如果 `warn` 的值是 `false`，那么组件则不会渲染: 

```js
function WarnningBanner(props){
    if(!props.warn){
        return null;
    }

    return (
        <div>
            Warning
        </div>
    );
}

class Page extends React.Component{
    constructor(props){
        super(props);
        this.state = {showWarning: true};
        this.handleToggleClick = this.handleToggleClick.bind(this);
    }

    handleToggleClick(){
        this.setState(state => ({
            showWarning: !state.showWarning
        }))
    }
    render(){
        return (
            <div>
                <WarnningBanner warn={this.state.showWarning}/>
                <button onClick={this.handleToggleClick}>
                    {this.state.showWarning? 'Hide': 'show'}
                </button>
            </div>
        )
    }
}
```

## 列表和key

### 基础列表组件

实现一个列表组件，这个组件接收`numbers`数组作为参数并输出一个元素列表

```js
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(<NumberList numbers={numbers} />, document.getElementById('root'));
```

这个例子会出现一个警告`Warning: Each child in an array or iterator should have a unique "key" prop.` 意思是当建一个元素时，必须包括一个特殊的 `key` 属性 

### key

- key 帮助 React 识别哪些元素改变了，比如被添加或删除，因此应当给数组中每一个元素赋予一个确定的标识
- 一个元素的 key 最好是这个元素在列表中拥有的独一无二的字符串，通常使用数据的 id 作为元素的 key
- 当元素没有确定 id 的时候，万不得已可以使用元素索引 index 作为 key。**如果列表项目的书序可能会变化，因此不建议使用索引作为 key 值，这样做会导致性能变差，还可能引起组件状态的问题
- 如果不显式指定 key 值，React 将默认使用索引用作列表项目的 key 值

### 用 key 提取组件

元素的 key 只有放在就近的数组上下文中才有意义

