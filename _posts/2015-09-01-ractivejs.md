---
published: true
layout: post
title: "Ractive.js学习小记"
date: 2015-09-01
---

以前听过MVC（Model View Controller）,但是第一次真正意义接触它是实习做项目的时候，而Ractive.js是一个经典的代表，也是相对比较好理解和上手的。  
总体思路：
一般会需要一个主文件比如`example.html` (里面含有用来渲染模板的容器）、一个模板文件比如`partial.html`、一个在主文件中引入的脚本文件比如`example.js`。

- example.html

```
<body>
<div class="container">
</div>
<srcipt src="example.js">
</body>
```
- partial.html

{% raw %}
```
<h1> Hello, {{name}} !</h1>
{{#if visible}}
这里有一只小青蛙 呱呱🐸
{{/if}}
<button on-click="show">Click!</button>
<ul>
{{#each lists}}
<li>{{name}},{{age}},{{city}}</li>
{{/each}}
</ul>
```
{% endraw %}

- example.js

```
var lists = [{
    name:Lee,
    age:21,
    city:Beijing
},{
    name:Mark,
    age:22,
    city:Shenzhen
},{
    name:Jack,
    age:24,
    city:Shanghai
}];
var ractive = new Ractive({
    el: $('.container'),
    template: require('partial.html'),
    data: { 
        name: 'world',
        visible：false,
        lists:lists
    } 
}); 
ractive.on('click', function() {
    ractive.set('visible',true);//点击按钮看到小青蛙
});
```
总体步骤就是在example.html中添加一个用来渲染模板的容器，编写模板文件并在脚本中实例化一个Ractive对象，在其中将**el**属性设置为相应的容器（在这里用Jquery获取id或class），**template**属性设置为需要的模板文件，而**data**属性则设置为模板中需要引入的具体值。通过**ractive.set()**可以实现数据的更新，而**ractive.on()**可以用来进行DOM事件绑定。上面的栗子中还可以看到对于List sections这样的结构，Ractive.js的用法也很方便，通常在和后端的交互中，比如调用接口获取到一组投资数据，就可以通过{{#each}} {{/each}}的方式在前端展现成列表的形式。

在使用Ractive.js的过程中，确实感受到MVC模式的好处，可以使耦合性低而不是牵一发而动全身，并且复用度提高，一些通用的部分就可以抽出来做成模板而减少工作量提高效率。


更多，详见 [Ractive.js]




[Ractive.js]:http://www.ractivejs.org/
