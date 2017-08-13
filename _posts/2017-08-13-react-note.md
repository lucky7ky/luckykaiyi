---
published: true
layout: post
title: "React 核心知识点学习笔记"
date: 2017-08-13
--- 

最近项目开发用到 React，重新复习了 React 的基础用法，查查官网的文档，也读了 medium 上关于 React 核心概念的一些文章。这里，或是摘录或是总结，把一些要点整理一下 ^_^。。

### 一、 Props vs State

React 当中的数据流是单向的：数据只能由上往下从父组件传向子组件，子组件就是通过 props 来获取父组件中的数据。state 则是由组件内部维护的, React 把组件看成是一个状态机（State Machines）。通过与用户的交互，实现不同的状态 （通过 `this.setState` 修改状态值)，从而渲染 UI。

#### 关于state， 需要注意：

- **Do Not Modify State Directly**

```
// ❎ This will not re-render a component:
this.state.comment = 'Hello';

// ✅ Instead, use setState():
this.setState({comment: 'Hello'});

// ❗️Remember: The only place where you can assign this.state is the constructor.
```
- **State Updates May Be Asynchronous**  
React 可能会对多个 setState 进行批量操作，`this.props` 和 `this.state` 的值不能保证同步更新，所以在计算新状态的时候不能依赖它们的值。具体看下面栗子👇

```
// ❎ This code may fail to update the counter：
this.setState({
  counter: this.state.counter + this.props.increment
});

// ✅ To fix it, use a second form of setState() that accepts a function rather than an object. That function will receive the previous state as the first argument, and the props at the time the update is applied as the second argument:
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));

// ✅ It also works with regular functions:
this.setState(function(prevState, props) {
  return {
    counter: prevState.counter + props.increment
  };
});
```
- **State Updates are Merged**  
当调用 setState() 时, React 会将新的状态（传进 setState() 中的 object）与现有的 state 合并。

```  
//For example, your state may contain several independent variables:
  constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
//Then you can update them independently with separate setState() calls:
  componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
//Note: The merging is shallow, so this.setState({comments}) leaves this.state.posts intact, but completely replaces this.state.comments.
```
### 二、 Components

### ✨ Functional vs Class 

声明组件的两种方式：函数式和类式 。

- **Functional Component**  
从 React 0.14 版本开始，可以创建无状态函数式组件。函数声明很简单，接收一个 props object 参数, 并返回一个 React element。

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
这种组件只负责根据传入的props来展示，不涉及 state 状态的操作。  
一般，当组件不需要复杂的交互和方法时，函数式的写法可以让代码更加简洁明了。并且，函数式组件中并不需要进行生命周期的管理与状态管理，因此 React 并不需要进行某些特定的检查或者内存分配，从而保证了更好的性能表现。

- **Class Component**  
 使用 ES6 class，如下：  
 
```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
官网上另一个更完整的栗子👇

```
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
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
由上面的栗子看到，Class Component 可以在 constructor 中初始化 state， 可以添加生命周期函数，可以通过 setState 方法操作 state，还可以声明一个组件的其他方法。

### ✨ Presentational vs Container

在实践过程中，组件逐渐被分为两种角色，一种关注数据交互，例如加载 server 端的数据，另一种只关注 UI 逻辑，主要与内容展示相关。前者被称作容器组件（Container），后者被称作展示组件（Presentational）。其他类似的名称 Fat vs Skinny, Smart vs Dumb, Stateful vs Pure, Screens vs Components 。。。

结合上面讲的 Functional Component 和 Class Component， 展示组件 Presentational 较多地采用无状态的函数式组件的写法。 而容器组件 Container 通常是具有状态的，并且为展示组件和其他的容器组件提供数据，由于涉及状态操作和其他方法，容器组件较多采用类式声明。


> In my experience, presentational components tend to be stateless 
> pure functions, and containers tend to be stateful pure classes. 
> However this is not a rule but an observation... 
> —— by Dan Abramov

关于 Presentational 和 Container 的详细对比，可以看 Redux 作者 Dan Abramov 的文章 [Presentational and Container Components]

...

#### 参考资料

[State and Lifecycle] 
   
[Components and Props]   
       
[Presentational and Container Components]  

[React’s Five Fingers of Death. Master these five concepts, then master React]

[Components and Props]:https://facebook.github.io/react/docs/components-and-props.html
[State and Lifecycle]: https://facebook.github.io/react/docs/state-and-lifecycle.html
[Presentational and Container Components]:https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0
[React’s Five Fingers of Death. Master these five concepts, then master React]: https://medium.freecodecamp.org/the-5-things-you-need-to-know-to-understand-react-a1dbd5d114a3