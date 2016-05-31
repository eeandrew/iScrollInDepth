这并不是一本iScroll入门书籍。当你阅读这本书籍时，我会假设你对iScroll的API已经有了基本的掌握，你知道怎么使用iScroll，至少知道下面的这些参数是什么意思。

{TODO iScroll基本参数}

在深入了解iScroll之前，我们先来看看iScroll的源码文档结构，然后学会怎么打包出iScroll文件。

我们知道，iScroll作者根据功能侧重点，为iScroll准备了5个发布版本。

iscroll-lite.js(iScroll体验版)是iScroll一个很精简的版本。只提供iScroll的一个核心功能：滚动。你可以通过它来了解iScroll的核心源码实现，事实上，本书的源码讲解也是从这个版本开始的。但是一般不推荐在项目中使用这个版本。你要知道，一个功能完整的iscroll库基本可以取代其他所有第三方的手势滚动和手势检测库。是不是很厉害！

isroll.js(iScroll基础版)是iScroll的一个基本功能比较完备的版本，一般来说这个版本可以满足你大部分的滑动需求。在你不知道应该使用哪个版本的情况下，使用它是不会错的。它的功能包括:

1. 基本的手势滚动
2. 鼠标滚轮(wheel)滚动控制
3. 键盘按键(keys)滚动控制
4. 对齐(snap)功能
5. 模拟滚动条(scroll bar)
6. 页码指示器(indicator)

iscroll-probe.js(iScroll检测版)在iscroll.js的基础上，多了一个像素位置探测的功能。根据你的设置，它最高可以做到单位像素滑动探测。当你需要根据滑动位置做一些操作时，比如经典的parallax效果，那么probe版本可以满足你的需求。除此之外，probe版本包含iscroll.js的所有功能。

iscroll-zoom.js(iScroll缩放版)顾名思义，可以用来做双指缩放控制，比如iPhone上经典的双指缩放图片效果。是不是很酷？别着急，我们会在后面的章节中给你讲解zoom双指缩放的实现原理。除此之外，zoom版本包含iscroll.js的所有功能。

iscroll-infinite.js(iScroll无限滚动版)，一个简单的解释就是用有限的DOM结构去展示无限的数据。没有理解吗？想象一下大家经常玩的微博应用，新鲜微博好像可以成千上万无限的刷。如果用HTML来实现这样的功能，是不是说我们需要构建成千上万的DOM列表项呢？当然不是。这个时候，使用iScroll无限滚动版本，就可以只用也许不到10个的DOM列表项，来展示成千上万的列表数据。别着急，在后面的章节中我们会仔细讲解无限滚动的实现原理。只要你有耐心看下去。

去github上下载iScroll源码，里面最要的文件当属build,src,demos这三个文件夹和build.js文件。
其中build文件夹里面是iScroll作者发布的各版本源码，他们都没有被压缩过。如果你要在项目中使用某个版本，请记得把他们压缩后在发布哦。
demos里面是iScroll各个强大功能的代表实例。你可以在浏览器当中运行这些例子，对iScroll的功能有个直观了解。当然，如果你对iScroll的某些配置不熟悉，也可以在demos里面找到参考实例。
src里面就是iScroll的源代码了，也是本书要和大家一起重点研究学习的地方。
src文件夹里代码结构如下:
## TODO src文件夹结构
src 
 |
 |__default
 |__indicator
 |__infinite
 |__keys
 |__probe

有三个文件需要我们注意： open.js,core.js,close.js。如果看一下这三个文件的内容，我们发现，他们都不是完整的函数模块，不能直接运行。这算是iScroll的一个特色吧。
这是open.js
```
(function (window, document, Math) {

```

这是close.js
```

IScroll.utils = utils;

if ( typeof module != 'undefined' && module.exports ) {
	module.exports = IScroll;
} else {
	window.IScroll = IScroll;
}

})(window, document, Math);
```
单独看这两个文件好像没有任何意义，但是当把他们的内容组合起来，似乎可以看出门道了
```
(function (window, document, Math) {
IScroll.utils = utils;
if ( typeof module != 'undefined' && module.exports ) {
	module.exports = IScroll;
} else {
	window.IScroll = IScroll;
}

})(window, document, Math);

```
原来iScroll的功能拆分不是通过常见的独立模块，而是通过将整个js文件按照头部，内容，和尾部来拆分。所以open.js(头)，close.js(尾)，core.js(内容)组成了Iscroll的基本骨架。然后根据需要打包的目标文件，和其他功能模块进行内容组合，就得到了最终的iScroll-xxx.js。
我们来尝试自己编译一个iscroll版本吧。首先删除build目录下的iscroll-lite.js文件。在iscroll根目录下，运行 node build.js lite。命令运行完成后，在build目录下又重新出现了iscroll-lite.js文件。
这个命令做的事情其实非常简单，就是将open.js, utils.js, core.js, default/_animate.js, default/handleEvent.js, close.js这些文件的内容按照顺序组合在一起。有了iscroll-lite.js文件，就让我们开始iScroll的源码解读之旅吧。

当然，你也可以试试打包其他版本的iscroll
试试这些命令，看看能得到什么结果吧。
node build.js iscroll
node build.js zoom
node build.js probe
node build.js infinite
node build.js dist

备注：iscroll其他版本的打包逻辑比lite版本要复杂一点，我们将在以后的章节中讲到[TODO]
