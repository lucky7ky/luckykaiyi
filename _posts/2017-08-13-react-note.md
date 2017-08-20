---
published: true
layout: post
title: "React 核心知识点学习笔记"
date: 2017-08-13
--- 

最近项目开发用到 React，重新复习了 React 的基础用法，查查官网的文档，也读了 medium 上关于 React 核心概念的一些文章。这里，或是摘录或是总结，把一些要点整理一下 ^_^。。

### 一、 Props vs State

React 当中的数据流是单向的：数据只能由上往下从父组件传向子组件，子组件就是通过 props 来获取父组件中的数据。state 则是由组件内部维护的, React 把组件看成是一个状态机（State Machines）。通过与用户的交互，实现不同的状态 （通过 `this.setState` 修改状态值)，从而渲染 UI。

#### 关于 state， 需要注意：

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

### 三、 Refs and the DOM

在标准的 React 数据流中，props 是父子组件之间交互的唯一方式。通过 props 的更新触发子组件的重新渲染。但某些情况下需要在数据流之外修改子组件（子组件可以是一个 DOM 元素或者一个 React component 的实例）。

React 提供了 ref 属性，ref 属性会获取一个回调函数, 回调函数接收 DOM 元素或者组件实例作为参数，回调函数在组件 mounted 或者 unmounted 后会被立即执行。

使用 ref 属性的一个典型场景是让输入框聚焦。看下面的官网小栗子👇

- **Adding a Ref to a DOM Element**

```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.focus = this.focus.bind(this);
  }
  focus() {
    this.textInput.focus();
  }
  render() {
    // Use the `ref` callback to store a reference to the text input DOM
    return (
      <div>
        <input
          type="text"
          ref={(input) => { this.textInput = input; }} />
        <input
          type="button" value="Focus the text input" 
          onClick={this.focus}
        />
      </div>
    );
  }
}

```
- **Adding a Ref to a Class Component**

```
class AutoFocusTextInput extends React.Component {
  componentDidMount() {
    this.textInput.focus();
  }
  render() {
    return (
      <CustomTextInput
        ref={(input) => { this.textInput = input; }} />
    );
  }
}
```
注意：上面栗子中的 CustomTextInput 必须是类式声明的， 如果是函数声明的组件则无效。函数组件没有实例，因而不支持 ref 属性。 

补充说明：this.refs.[refName] 这种形式的早期用法，官方不建议使用了，作为遗留的 API , 可能会在以后的版本中被移除。

### 四、Higher-Order Components （HOCs)

高阶组件（HOC) 本身并不属于 React API 的一部分，而是由 React 的组合特性衍生出来的一种设计模式。HOC 本质上是一个函数，接收一个组件，并返回一个新的组件。

```
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```
使用 HOC 可以更好地重用组件逻辑。可以看官网文档中的代码示例，方便了解 HOC 的使用场景及具体实现 ：[Use HOCs For Cross-Cutting Concerns]

❗️需要注意的是，不要在render方法内部使用高阶组件。

React的 diff 算法会根据组件标识来决定是应该更新其现存的子树，还是更替自身。如果组件render方法的返回值跟之前的返回值相同，则将两者的子树递归对比更新。如果不相同，之前的子树被新子树完全替换。

```
render() {
  // A new version of EnhancedComponent is created on every render
  // EnhancedComponent1 !== EnhancedComponent2
  const EnhancedComponent = enhance(MyComponent);
  // That causes the entire subtree to unmount/remount each time!
  return <EnhancedComponent />;
}
```

#### ✨React 中两种 HOC 的实现方法

- **Props Proxy (PP)**  
Props Proxy (PP) 的最简实现：

```
function ppHOC(WrappedComponent) {
  return class PP extends React.Component {
    render() {
      return <WrappedComponent {...this.props}/>
    }
  }
}
```
上述代码的 render 方法中返回了一个 WrappedComponent 类型的 React Element，并且传入了 HOC 接收到的 props，这就是 Props Proxy 的名字由来。

- **Inheritance Inversion (II)**  
Inheritance Inversion (II) 的最简实现：

```
function iiHOC(WrappedComponent) {
  return class Enhancer extends WrappedComponent {
    render() {
      return super.render()
    }
  }
}
```

返回的 HOC 类（Enhancer）继承了 WrappedComponent, 而不是 WrappedComponent 继承 Enhancer。在这种方式中，它们的关系看上去被反转（inverse）了, 所以称之为 Inheritance Inversion。

关于 Props Proxy 和 Inheritance Inversion 的具体用途和实现示例，可以看 franleplant 写的这篇文章：[React Higher Order Components in depth] 


#### 参考资料

[Components and Props]  

[Higher-Order Components]
       
[Presentational and Container Components]  

[Refs and the DOM] 

[React’s Five Fingers of Death. Master these five concepts, then master React]

[React Higher Order Components in depth]

[State and Lifecycle] 



[Components and Props]:https://facebook.github.io/react/docs/components-and-props.html
[State and Lifecycle]: https://facebook.github.io/react/docs/state-and-lifecycle.html
[Presentational and Container Components]:https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0
[React’s Five Fingers of Death. Master these five concepts, then master React]: https://medium.freecodecamp.org/the-5-things-you-need-to-know-to-understand-react-a1dbd5d114a3
[Refs and the DOM]: https://facebook.github.io/react/docs/refs-and-the-dom.html
[Higher-Order Components]: https://facebook.github.io/react/docs/higher-order-components.html
[Use HOCs For Cross-Cutting Concerns]: https://facebook.github.io/react/docs/higher-order-components.html
[React Higher Order Components in depth]: https://medium.com/@franleplant/react-higher-order-components-in-depth-cf9032ee6c3e
