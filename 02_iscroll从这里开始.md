本章我们要开启iscroll源码的神奇之旅。其实和其他手势库一样，iscroll的逻辑也是围绕touchestart,touchmove,touchend/touchcancel这三个事件。

首先，我们来探索iscroll的事件处理逻辑。为了更清楚的了解iscroll的事件处理逻辑，我们现将其它无关代码忽略或简化。

```
function IScroll(el,options) {
	this.options = options || {};
	this.wrapper = el;
	this.scroller = el.children[0];
	this._init();
}
```
先简单解释一下这段代码。