# JQuery-CodeAnalytic

JS是单线程语言

    (function(global, factory) {

                  /*
    这里是为了兼容node.js、sea-JS等符合CommonJS规范或类CommonJS规范的js框架
    module.export和export是node.js中用来创建模块的方法，如果条件成立，执行下面语句来兼容node.js
    （就是利用形参factory做中间人，来把JQ的各个功能模块用node.js创建模块的方法创建起来）
  */

if (typeof module === "object" && typeof module.exports === "object") {  
//查看 环境是否支持window.document属性
        module.exports = global.document  //支持环境window。document属性那么就执行
      ? factory(global, true)
      : function(w) {   //不支持window.document环境执行,写个函数扔个Error说这环境不适用JQ，但依旧返回JQ的功能函数（但大部分估计是不能用的了）
          if (!w.document) {  
            throw new Error("jQuery requires a window with a document");
          }
          return factory(w);
        };
  } else {
    factory(global);  //以上代码都不成立的情况下，就是直接引入JQ那一万多行的功能函数即可
  }

    })(typeof window !== "undefined" ? window : this , function(window, noGlobal) {
              //一万多行的功能函数
    });

    这是一个匿名函数，形参(global, factory)
    实参(typeof window !== "undefined" ? window : this ,function(window, noGlobal) {})
        参数一： typeof window !== "undefined" ? window : this
               		 是一个三目运算符，判断当前执行环境是否支持window类型，是的话返回window 否则返回this
        参数二： function(window, noGlobal) {}
是一个函数，里面包含了一万多行的JQ功能函数

DOM文档加载的步骤：
(1) 解析HTML结构。
(2) 加载外部脚本和样式表文件。
(3) 解析并执行脚本代码。
(4) 构造HTML DOM模型。//ready
(5) 加载图片等外部文件。
(6) 页面加载完毕。//load

在javascript中一共有四种上下文调用方式：
方法调用模式、函数调用模式、构造器调用模式、apply调用模式

 jQuery.extend调用的时候上下文指向的是jQuery构造器
         jQuery.fn.extend调用的时候上下文指向的是jQuery构造器的实例对象了

arguments:
arguments对象不是一个 Array 。它类似于Array，但除了length属性和索引元素之外没有任何Array属性
arguments是一个对象  对象可以是任何数据类型
arguments 对象只能在函数内使用
将转为数组的方法：
var args = Array.prototype.slice.call(arguments);
var args = [].slice.call(arguments);

// ES2015
const args = Array.from(arguments);

将arguments转为真正的数组：
var args = Array.from(arguments);
var args = [...arguments];

arguments的属性：
arguments.callee
指向当前执行的函数。

arguments.caller 
指向调用当前函数的函数。

arguments.length
指向传递给当前函数的参数数量。

arguments[@@iterator]
返回一个新的Array迭代器对象，该对象包含参数中每个索引的值。

 jQuery.extend()和 jQuery.fn.extend() 区别：
1.两者调用方式不同：
 jQuery.extend(),一般由传入的全局函数来调用，主要是用来拓展个全局函数，如$.init()，$.ajax();
 jQuery.fn.extend(),一般由具体的实例对象来调用，可以用来拓展个选择器，例如$.fn.each();

2.两者的主要功能作用不同：
jQuery.extend(object); 为扩展jQuery类本身，为自身添加新的方法。
        jQuery.fn.extend(object);给jQuery对象添加方法

迭代器：
迭代器是一个框架的重要设计。我们经常需要提供一种方法顺序用来处理聚合对象中各个元素，而又不暴露该对象的内部，这也是设计模式中的迭代器模式(Iterator)。
jQuery中的$.each方法就是一个典型的迭代器，通过each我们可以传入额外的function，然后来对所有的item项进行迭代操作

迭代器特点：
☑ 访问一个聚合对象的内容而无需暴露它的内部。
☑ 为遍历不同的集合结构提供一个统一的接口，从而支持同样的算法在不同的集合结构上进行操作。
☑ 遍历的同时更改迭代器所在的集合结构可能会导致问题。
简单的说：封装实现，然后迭代器的聚合对象不用关心迭代的过程，从而符合SRP原则。

回调函数设计：
once的设计：
once的作用确保回调列表只执行（.fire()）一次

memory的设计：
memory：保持以前的值，将添加到这个列表的后面的最新的值立即执行调用任何回调

unique的设计：
Unique：确保一次只能添加一个回调(所以在列表中没有重复的回调)

stopOnFalse：
stopOnFalse: 当一个回调返回false 时中断调用

内存泄露:
什么是内存泄露？
内存泄露是指一块被分配的内存既不能使用，又不能回收，直到浏览器进程结束

常见内存泄露的几种情况：
1.循环引用
2.Javascript闭包
3.DOM插入

我们常见的异步操作：
setTimeout定时器
postmessage
WebWorkor
CSS3 动画
XMLHttpRequest
HTML5的本地数据
等等…

Deferred延迟
Deferred 提供了一个抽象的非阻塞的解决方案（如异步请求的响应），它创建一个promise对象，其目的是在未来某个时间点返回一个响应。简单来说就是一个异步/同步回调函数的处理方案。

$.Deferred在jQuery代码内部有四个模块被使用，分别是:
“promise方法”、
“DOM ready”、
“Ajax模块”及
“动画模块”。