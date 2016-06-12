IScroll最为动人之处就是它带来的如丝滑般流畅的滚动效果。滚动是IScroll的核心，本章就让我们一起了解IScroll的滚动原理和具体实现。

首先，我们考虑一下如果不用IScroll，我们怎么实现滚动呢？很简单，只要一下3步，
1. 父元素的高度或宽度固定
2. 父元素的溢出属性overlow设置为scroll
3. 子元素的高度或者宽度大于父元素的高度或者宽度

```
#wrapper {
	height: 50px;
	width: 200px;
	overflow:scroll;
}

#scroller {
	height:50px;
	width:1000px; //宽度大于父元素，因此可以x轴滚动
}
<div id="wrapper">
	<div id="scroller"></div>	
</div>
```
通过上面的简单例子，我们就得到了一个元素内滚动的代码。然而，如果我们深入理解一下对滚动的需求，我们会发现上面的例子满足不了我们的需求。

1. 我们需要知道scroller滚动了多少像素
4. 我们需要被通知scroller每个像素的滚动
3. 我们需要能够控制scroller滚动的位置
2. 我们需要scroller有边界减速的效果
5. ...



IScroll能够满足上面的需求，并且核心就在_move函数里面。那么，我们就来看看_move函数究竟做了什么。
_move是touchmove事件的监听函数，所以它的形式如下:

```
_move: function(e) {
 
}
```
再次回忆一下第二章中讲到的IScroll滚动原理。直观上我们看到scroller会随着手指滚动，可能很容易有这样的误解，那就是touch事件的监听对象是scroller，我们只要把touch事件的位置信息交给scroller就可以了。然而这样带来的问题是scroller没有了边界，或者说scroller的边界变成了整个浏览器可视区域(pointX和pointY是相对于浏览器可视区域左上角点的坐标值)。很多情况下，这不是我们想要的。IScroll的原理非常精妙。将scroller包裹在wrapper里，wrapper就成了scroller的边界。【todo 继续解释】

[配图 touch监听在scroller上面的问题]

一开始，_move函数就定义使用了一堆变量，我们逐个分析:

```
var point		= e.touches ? e.touches[0] : e,
	deltaX		= point.pageX - this.pointX,
	deltaY		= point.pageY - this.pointY,
	timestamp	= utils.getTime(),
	newX, newY,
	absDistX, absDistY;

	this.pointX		= point.pageX;
	this.pointY		= point.pageY;

	this.distX		+= deltaX;
	this.distY		+= deltaY;
	absDistX		= Math.abs(this.distX);
	absDistY		= Math.abs(this.distY);
```
point记录了此时手指在wrapper上的位置信息。

 