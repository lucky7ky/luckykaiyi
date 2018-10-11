---
published: true
layout: post
title: "Hello, LeetCode"
date: 2015-08-25
---
据说太久没写博客会长草。  
我果然还是太懒了。。

---------
\<!-- 题外话 begin -->
 
开始正文前还是习惯先交代一下背景。距离上一篇博客都超过三个月了，时间过得简直太快，这段时间都在CC公司实习，做的是前端开发，学习了不少东西，鉴于本文的标题是‘Hello, LeetCode'，就不细聊啦，之后有时间再总结成一篇博客吧（其实一直有在想。但始终没实践。。😪）  

\<!-- 题外话 end -->

###正文 》》
这篇博客主要纪念一下第一次刷LeetCode [JS]的过程和感悟吧，时间其实是上周日，不是科班出身的我之前也听说过别人所谓的‘刷题’，当时觉得算法、数据结构等等对于我还太陌生，but anyway, 现在是时候需要开始练习了。  
在wyy的带领下，开启了LeetCode的第一题，其实一开始想选最最Easy的，眼花选了个Medium的,可能因为序号排第一。  
是它 是它 就是它 👉 [Two Sum]  

<img src="http://images.yanyiwu.com/TwoSum.png">

看完题目，心里好像放松了一些，之前完全不知道做这种题目是什么概念，能看懂题目意思，至少一下子亲切了许多。  
好吧，接来下let me try , 打开小黑屏幕，思考和尝试中敲定如下代码：
  
```  
var twoSum = function (nums,target) {
    for (var i = 0 ; i < nums.length; i++) {
        for (var j = i + 1; j < nums.length; j++) {
            if (target == nums[i] + nums[j]) {
                var output = [i + 1, j + 1];
                return output;
            }
        }
    }
}
```
自己随便弄了个例子测试下，console出来的结果完全符合预期呀！！   
心里一顿窃喜。( ⊙o⊙ ) 第一道题就这么解出来了？！跟WYY炫耀我已经成功解出来了，他瞄了一眼代码，笑了🐸，让我提交试试看，我迫不及待地Submit Solution, 然后... 😱
<img src = "http://images.yanyiwu.com/74905FF0-A77F-41F2-A820-6C42F158420B.png">
这是什么鬼。WYY又笑了🐸，那个笑容和之前他看到我写的代码一样，好吧，一切在他预料之中👌。

之后，WYY开启教学模式。开始讲时间复杂度O(1) 和O(N)，我原来的写法是O(N^2) ，如果数组很长的话，将会非常耗时，从性能来讲的话不是好的实现方式，所以需要再改改。

然后，我想了很久换了一种写法，去掉了里面的那层for循环。代码如下：   

```
var twoSum = function (nums,target) {
    var values = [];
    for (var i = 0 ; i < nums.length; i++) {
        var j = nums.indexOf(target - nums[i]);
        if (j != -1) {
            var output = [i + 1, j + 1]
            return output;
        }
    }
}
```
我以为这次是完美的，但实际上 is not 。提交后结果还是和之前一样，超时了。看到结果的瞬间感觉不相信代码了。后来恍然大悟才知道indexOf本身也是O(N)，所以时间复杂度整体还是O(N^2)，没有提升。看来，并没有我想的那么简单。  

在WYY的各种点拨指引下，有了新的思路，总的目标就是把时间复杂度由O(N^2)减小为O(N)。还是用了两个for循环，只不过这次不是嵌套关系了。  

```
var twoSum = function (nums,target) {
    var values = {};
    for (var i = 0 ; i < nums.length; i++) {
        values [nums[i]] = i;
    }
    for (var i = 0; i < nums.length; i++) {
        if ((target - nums[i]) in values && i != values[(target - nums[i])]) {
            var output = [i + 1, values[(target-nums[i])]+ 1];
            return output;
        }
    }
}
```
哈 ^_^ 终于pass了。必须截图纪念一下。
<img src = "http://images.yanyiwu.com/2D`[[~7CV60U9SD35LNK365.jpg">

<img src = "http://images.yanyiwu.com/9~XE%258~@8XQ%253%25N`YF$M~NN.jpg">
耗时在分布图里面排在中等吧。本来这个截完图后除了心情依然兴奋也就没啥事了。看了下最新的写法，发现代码里面有个细节，有一些重复的地方可以优化一下，说不定还能把时间再缩短一点。

```
var twoSum = function(nums, target) {
    var values = {};
    for (var i = 0 ; i < nums.length; i++) {
        values [nums[i]] = i;
    }
    for (var i = 0; i < nums.length; i++) {
        var value = target - nums[i];
        if (value in values && i != values[value]) {
            var output = [i + 1, values[value]+ 1];
            return output;
        }
    }
};
```
好奇地提交看结果，惊喜地发现这样稍微一改，分布图排名提高了不少。

<img src = "http://images.yanyiwu.com/S5%25ZJH)WCWWT(MW~$L3%25IYV.jpg">

不过最关键的还是由O(N^2)到O(N)的那次改进。那也是做这道题过程中思考最久的部分，也多亏了WYY的启发。 


除了这道 [TWO SUM] ,后来又选了道Easy模式的 [Add Digits]（这道题感觉主要难点就是找规律吧，有点数学逻辑题的感觉）。目前只开始做了这两道，希望以后能够持之以恒，慢慢积累，碰到有趣的题目可以在这里记录一下 📒。


 

[Two Sum]:https://leetcode.com/problems/two-sum/
[Add Digits]:https://leetcode.com/problems/add-digits/
