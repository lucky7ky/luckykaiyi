---
published: true
layout: post
title: "使用JavaScript的注意事项和一些技巧"
date: 2015-04-27
--- 

### Remember >> 
🌲注意不要在文档加载之后使用 document.write()。这会覆盖该文档。  

🌲如果前缀为 0，则 JavaScript 会把数值常量解释为八进制数，如果前缀为 0 和 "x"，则解释为十六进制数。  
```
var y=0377;
var z=0xFF;  
```  
提示：绝不要在数字前面写零，除非您需要进行八进制转换。  

🌲为日期对象设置了一个特定的日期 (2008 年 8 月 9 日)： 
 
```
var myDate=new Date()  
myDate.setFullYear(2008,7,9)
```
注意：表示月份的参数介于 0 到 11 之间。也就是说，如果希望把月设置为 8 月，则参数应该是 7。  

🌲如果逻辑对象无初始值或者其值为 0、-0、null、""、false、undefined 或者 NaN，那么对象的值为 false。否则，其值为 true（即使当自变量为字符串 "false" 时）！  
🌲slice() 方法可从已有的数组中返回选定的元素。
`arrayObject.slice(start,end)` 
返回一个新的数组，包含从 start 到 end（不包括end所在的元素！！）的 arrayObject 中的元素。  
注意，该方法并不会修改数组，而是返回一个子数组。如果想删除数组中的一段元素，应该使用方法 Array.splice()。


### Some Tips ✨ 
🌲删除已有的 HTML 元素，首先要获得该元素的父元素。一种便捷的做法是使用其parentNode 属性来找到父元素：

```
var child=document.getElementById("p1");
child.parentNode.removeChild(child);
```



###🐱不断补充。。未完待续。。
