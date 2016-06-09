---
published: true
layout: post
title: "『React 开发』学习用 Ant Design 做后台系统"
date: 2016-06-04
---

背景：最近工作的一个需求是搭建一个后台系统，闫老师推荐说可以试下蚂蚁金服团队出品的 [Ant Design] 。看了一下官网的文档和示例，感觉好强大。Ant Design 基于 React 的组件化开发模式，封装了一套丰富而实用的 UI 组件库，特别适合用于开发后台系统这样的场景。  
在正式开发之前先学习文档，写个简单的 demo，感觉 Ant Design 还是比较好上手，亲测好用，值得安利 ~ ~

下面，把重要的知识点和写 demo 的过程整理记录一下 。

### 关于 React

由于 Ant Design 是基于 React 开发的， 所以第一步就是先熟悉 React 的开发模式。关于 React ，可以参考[一看就懂的ReactJs入门教程]，这篇文章介绍得很详细 👍。

📒这里，摘录一些学习要点：

#### ✨React 的两大特点：虚拟DOM 和 组件化开发

**虚拟DOM** —— React 引入了虚拟 DOM（Virtual DOM）的机制：在浏览器端用 Javascript 实现了一套 DOM API。基于 React 进行开发时所有的 DOM 构造都是通过虚拟 DOM 进行，每当数据变化时，React 都会重新构建整个 DOM 树，然后 React 将当前整个 DOM 树和上一次的 DOM 树进行对比，得到 DOM 结构的区别，然后仅仅将需要变化的部分进行实际的浏览器 DOM 更新。

**组件化开发** —— React 不是一个完整的 MVC 框架，最多可以认为是 MVC 中的 V（View）。MVC 的思想让你做到视图-数据-控制器的分离，React 推荐以组件的方式去重新思考 UI 构成，将 UI 上每一个功能相对独立的模块定义成组件，然后将小的组件通过组合或者嵌套的方式构成大的组件，最终完成整体 UI 的构建。

#### ✨JSX 

React 独有的 JSX 语法， 最直观的特点就是允许 HTML 与 JavaScript 的混写。JSX 和 JavaScript 不兼容， 凡是使用 JSX 的地方要注意`<script type="text/jsx">`。 React 提供的JSXTransformer.js 的作用就是将 JSX 语法转为 JavaScript 语法。  
❗️但是需要注意：React 不是一个新的模板语言，JSX 只是一个表象，没有 JSX 的 React 也能工作。  
❗️React 并不依赖 jQuery，当然我们可以使用 jQuery，但是 render 里面第二个参数必须使用 JavaScript 原生的 getElementByID 方法，不能使用 jQuery 来选取 DOM 节点。

#### ✨组件

React 允许将代码封装成组件（component），然后像插入普通 HTML 标签一样，在网页中插入这个组件。React.createClass 方法就用于生成一个组件类。  

Remember：

- 获取属性的值用的是 this.props.属性名
- 访问 state 的方法是 this.state.属性名
- getInitialState 函数必须有返回值，可以是 NULL 或者一个对象
- 组件的生命周期分成三个状态：  
  Mounting：已插入真实 DOM  
  Updating：正在被重新渲染  
  Unmounting：已移出真实 DOM  
- 添加外部 css 样式时，类名应该写成 className 而不是 class
- 添加内部样式时，应该是 style={{width: 200}} 而不是 style="width: 200" 
- 组件名称首字母必须大写
- 变量名用 {} 包裹，且不能加双引号

### Ant Design Demo

了解 React 的开发模式和基本语法后，接下来按照 [Ant Design] 官网文档的说明，快速上手编写 Demo。



#### Step 1：安装脚手架工具 （Node.js 需要 v4.x 或以上）


`$ npm install antd-init -g` 

#### step 2：创建项目&初始化

`$ mkdir ant-design-demo && cd ant-design-demo`  

运行 `$ antd-init`  

这时候会出现下面的提示  

```
? Please select boilerplate type (Use arrow keys)
❯ plain react - for simple project
  redux - for complex project
```
这里可以手动选择 plain-react 。

或者在刚才初始化时候直接加上类型 

`$ antd-init --type plain-react`

通过以上的命令，antd-init 就开始自动安装 npm 依赖了。耐心等待装完 ~ ~

安装完之后，整个项目的基本框架就搭建起来了。  
脚手架会生成一个 Todo 应用实例。  
运行 `$ npm start` 后访问 `http://127.0.0.1:8989`查看效果。

#### step 3：开发 

进入 src 目录开始进行开发 

```
// 以下为 src 的初始目录结构 
├── components
│   ├── App.jsx
│   ├── NotFound.jsx
│   ├── NotFound.less
│   └── Todos
│       ├── Todo.jsx
│       ├── Todo.less
│       ├── Todos.jsx
│       └── Todos.less
├── entries
│   ├── index.html // 入口文件
│   ├── index.js
│   └── index.less
├── layouts
│   └── MainLayout
│       ├── MainLayout.jsx
│       └── MainLayout.less
├── routes
│   └── index.js // 设置路由
└── services
    ├── todos.js
    └── xFetch.js
```
尝试使用 Ant Design 提供的各种组件。比如，导航、表单、表格等等，基本上一般后台系统需要的，都能找到封装好的组件，用起来很方便。这样在实际开发的时候就主要 focus on 业务逻辑，纯前端的细节交互大多已经在组件里实现好了，而且可以看到 Ant Design 提供的组件还是比较重视设计感和用户体验的 ~ ~




最后， 🐱[GitHub仓库地址]



[GitHub仓库地址]:https://github.com/luckykaiyi/ant-design-demo


[一看就懂的ReactJs入门教程]:http://www.cocoachina.com/webapp/20150721/12692.html

[Ant Design]:http://ant.design

