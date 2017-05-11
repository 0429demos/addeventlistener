# addeventlistener
js事件监听器用法实例详解

作者：天使小宝 字体：[增加 减小] 类型：转载 时间：2015-06-01 我要评论
这篇文章主要介绍了js事件监听器用法,以实例形式较为详细的分析了javascript事件监听器使用注意事项与相关技巧,需要的朋友可以参考下

本文实例讲述了js事件监听器用法。分享给大家供大家参考。具体分析如下：
1、当同一个对象使用.onclick的写法触发多个方法的时候，后一个方法会把前一个方法覆盖掉，也就是说，在对象的onclick事件发生时，只会执行最后绑定的方法。而用事件监听则不会有覆盖的现象，每个绑定的事件都会被执行。如下：
?
1
2
3
4
5
6
7
8
9
10
11
12
window.onload = function(){ 
 var btn = document.getElementById("yuanEvent"); 
 btn.onclick = function(){ 
  alert("第一个事件"); 
 } 
 btn.onclick = function(){ 
  alert("第二个事件"); 
 } 
 btn.onclick = function(){ 
  alert("第三个事件"); 
 } 
}
最后只输出：第三个事件，因为后一个方法都把前一个方法覆盖掉了。
原生态的事件绑定函数addEventListener：
?
1
2
3
4
5
6
7
8
9
10
11
12
var eventOne = function(){ 
 alert("第一个监听事件"); 
} 
function eventTwo(){ 
 alert("第二个监听事件"); 
} 
window.onload = function(){ 
 var btn = document.getElementById("yuanEvent"); 
 //addEventListener：绑定函数 
 btn.addEventListener("click",eventOne); 
 btn.addEventListener("click",eventTwo); 
}
输出：第一个监听事件 和 第二个监听事件
2、采用事件监听给对象绑定方法后，可以解除相应的绑定，写法如下：
?
1
2
3
4
5
6
7
8
9
10
11
12
var eventOne = function(){ 
 alert("第一个监听事件"); 
} 
function eventTwo(){ 
 alert("第二个监听事件"); 
} 
window.onload = function(){ 
 var btn = document.getElementById("yuanEvent"); 
 btn.addEventListener("click",eventOne); 
 btn.addEventListener("click",eventTwo); 
 btn.removeEventListener("click",eventOne); 
}
输出：第二个监听事件
3、解除绑定事件的时候一定要用函数的句柄，把整个函数写上是无法解除绑定的。
错误写法：
?
1
2
3
4
5
6
btn.addEventListener("click",function(){ 
 alert(11); 
}); 
btn.removeEventListener("click",function(){ 
 alert(11); 
});
正确写法：
?
1
2
btn.addEventListener("click",eventTwo); 
btn.removeEventListener("click",eventOne); 
总结：对函数进行封装后的监听事件如下，兼容各大主流浏览器。
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
/* 
 * addEventListener:监听Dom元素的事件 
 * 
 * target：监听对象 
 * type：监听函数类型，如click,mouseover 
 * func：监听函数 
 */
function addEventHandler(target,type,func){ 
 if(target.addEventListener){ 
  //监听IE9，谷歌和火狐 
  target.addEventListener(type, func, false); 
 }else if(target.attachEvent){ 
  target.attachEvent("on" + type, func); 
 }else{ 
  target["on" + type] = func; 
 } 
} 
/* 
 * removeEventHandler:移除Dom元素的事件 
 * 
 * target：监听对象 
 * type：监听函数类型，如click,mouseover 
 * func：监听函数 
 */
function removeEventHandler(target, type, func) { 
 if (target.removeEventListener){ 
  //监听IE9，谷歌和火狐 
  target.removeEventListener(type, func, false); 
 } else if (target.detachEvent){ 
  target.detachEvent("on" + type, func); 
 }else { 
  delete target["on" + type]; 
 } 
} 
var eventOne = function(){ 
 alert("第一个监听事件"); 
} 
function eventTwo(){ 
 alert("第二个监听事件"); 
} 
window.onload = function(){ 
 var bindEventBtn = document.getElementById("bindEvent"); 
 //监听eventOne事件 
 addEventHandler(bindEventBtn,"click",eventOne); 
 //监听eventTwo事件 
 addEventHandler(bindEventBtn,"click",eventTwo ); 
 //监听本身的事件 
 addEventHandler(bindEventBtn,"click",function(){ 
  alert("第三个监听事件"); 
 }); 
 //取消第一个监听事件 
 removeEventHandler(bindEventBtn,"click",eventOne); 
 //取消第二个监听事件 
 removeEventHandler(bindEventBtn,"click",eventTwo); 
} 
实例：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
<!DOCTYPE html> 
<html> 
 <head> 
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"> 
  <title>Event</title> 
  <script type="text/javascript"> 
   function addEventHandler(target,type,func){ 
    if(target.addEventListener){ 
     //监听IE9，谷歌和火狐 
     target.addEventListener(type, func, false); 
    }else if(target.attachEvent){ 
     target.attachEvent("on" + type, func); 
    }else{ 
     target["on" + type] = func; 
    } 
   } 
   function removeEventHandler(target, type, func) { 
    if (target.removeEventListener){ 
     //监听IE9，谷歌和火狐 
     target.removeEventListener(type, func, false); 
    } else if (target.detachEvent){ 
     target.detachEvent("on" + type, func); 
    }else { 
     delete target["on" + type]; 
    } 
   } 
   var eventOne = function(){ 
    alert("第一个监听事件"); 
   } 
   function eventTwo(){ 
    alert("第二个监听事件"); 
   } 
   window.onload = function(){ 
    var bindEventBtn = document.getElementById("bindEvent"); 
    //监听eventOne事件 
    addEventHandler(bindEventBtn,"click",eventOne); 
    //监听eventTwo事件 
    addEventHandler(bindEventBtn,"click",eventTwo ); 
    //监听本身的事件 
    addEventHandler(bindEventBtn,"click",function(){ 
     alert("第三个监听事件"); 
    }); 
    //取消第一个监听事件 
    removeEventHandler(bindEventBtn,"click",eventOne); 
    //取消第二个监听事件 
    removeEventHandler(bindEventBtn,"click",eventTwo); 
   } 
  </script> 
 </head> 
 <body> 
  <input type="button" value="测试" id="bindEvent"> 
  <input type="button" value="测试2" id="yuanEvent"> 
 </body> 
</html>
PS：这里再为大家提供一个关于JS事件的在线工具，归纳总结了JS常用的事件类型与相关函数功能：
javascript事件与功能说明大全：
http://tools.jb51.net/table/javascript_event
希望本文所述对大家的javascript程序设计有所帮助。
