# css3 animation

***

###  如何定义和调用动画
<pre><code>.myClass{
-webkit-transform:rotate(90deg); //使旋转结束后保持旋转的角度不变
-webkit-animation: animation 6s; //调用animation动画 在6秒内执行结束
}</code></pre>

<pre><code>@-webkit-keyframes animation{
from { -webkit-transform: rotate(0deg) }
to { -webkit-transform: rotate(3330deg) } //定义旋转动画

//也可以通过百分比指定各个时间执行什么动画
@-webkit-keyframes animation{
0%{ left: 60px; opacity: 0;}
86% { left: 60px; opacity: 0;}
100% { left: 110px; opacity: 1;}
}
}</code></pre>

### js 调用动画
<pre><code>$(el).addClass("myClass"); // so easy
</pre></code>
### js 捕获动画事件
<pre><code> 1、-webkit-animation动画其实有三个事件：　　　　　　
　　　　　　开始事件 webkitAnimationStart
　　　　　　结束事件  webkitAnimationEnd
　　　　　　重复运动事件 webkitAnimationIteration
　2、  css3的过渡属性transition，在动画结束时，也存在结束的事件：webkitTransitionEnd;
　　　　　　注意：transition,也仅仅有这一个事件。
　　　
 var tt =  $(el).find('.myCanvas')[0];
 tt.addEventListener("webkitAnimationStart", function(){
 //动画开始时事件
    console.log(1);　　//动画开始时，控制台输出1
 }, false);
 tt.addEventListener("webkitAnimationEnd", function(){
 //动画重复运动时的事件
 console.log(3);　　//第一遍动作完成时，控制台输出3
 }, false);
</pre></code>


### 多次执行同一个 animation

需要在addClass之前先removeClass。
但又不能直接
<pre><code>removeClass(myClass);
addClass(myClass);
</pre></code>

需要有一点时间间隔
比如在“webkitAnimationEnd”事件触发时移除class，
或者
<pre><code>removeClass(myClass);
setTimeout(function(){
addClass(myClass);
},50);//时间好像为0都可以
</pre></code>

###一段实用的事件兼容代码

<pre><code>
//动画事件兼容
var VENDORS = ["Moz",'webkit','ms','O'];
var TRANSITION_END_NAMES = {
    "Moz" : "transitionend"
    ,"webkit" : "webkitTransitionEnd"
    ,"ms" : "MSTransitionEnd"
    ,"O" : "oTransitionEnd"
}
var ANIMATION_END_NAMES = {
    "Moz" : "animationend"
    ,"webkit" : "webkitAnimationEnd"
    ,"ms" : "MSAnimationEnd"
    ,"O" : "oAnimationEnd"
}
var ANIMATION_START_NAMES = {
    "Moz" : "animationStart"
    ,"webkit" : "webkitAnimationStart"
    ,"ms" : "MSAnimationStart"
    ,"O" : "oAnimationStart"
}
var css3Prefix,TRANSITION_END_NAME,ANIMATION_END_NAME,ANIMATION_START_NAME;
var mTestElement = document.createElement("div");

for (var i = 0,l = VENDORS.length; i < l; i++) {
    css3Prefix = VENDORS[i];
    if ((css3Prefix + "Transition") in mTestElement.style) {
        break;
    }
    css3Prefix = false;
}

if(css3Prefix) {
    TRANSITION_END_NAME = TRANSITION_END_NAMES[css3Prefix];
    ANIMATION_END_NAME = ANIMATION_END_NAMES[css3Prefix];
    ANIMATION_START_NAME = ANIMATION_START_NAMES[css3Prefix];
}
</pre></code>

***一些常见的api***

rotate(360deg) 旋转

scale(1,1) 放大缩小比例

translate(x,y) 平移

skew() 坐标系旋转？

    div {
        -ms-transform: skew(30deg,20deg); /* IE 9 */
        -webkit-transform: skew(30deg,20deg); /* Chrome, Safari, Opera */
        transform: skew(30deg,20deg);
    }

matrix() 包括以上所有的样子，估计用不到

    div {
        -ms-transform: matrix(0.866,0.5,-0.5,0.866,0,0); /* IE 9 */
        -webkit-transform: matrix(0.866,0.5,-0.5,0.866,0,0); /* Chrome, Safari, Opera */
        transform: matrix(0.866,0.5,-0.5,0.866,0,0);
    }