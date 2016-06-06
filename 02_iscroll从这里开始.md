本章我们要开启iscroll源码的神奇之旅。其实和其他手势库一样，iscroll的逻辑也是围绕touchestart,touchmove,touchend/touchcancel这三个事件。

[配图 介绍手指滑动和这个三个事件的时间节点关系]

滑动事件都可以分解为这三个动作
1. 手指接触屏幕
2. 手指在屏幕滑动
3. 手指离开屏幕
这三个动作就引发了相应的touch事件: touchstart, touchmove, touchcancel/touchend。

首先，我们来探索iscroll的事件处理逻辑。为了更清楚的了解iscroll的事件处理逻辑，我们现将其它无关代码忽略或简化。

```
function IScroll(el,options) {
	this.wrapper = typeof el == 'string' ? document.querySelector(el) : el;
	this.scroller = el.children[0];
	this.options = {
	};
	this._init();
}
```
先简单解释一下这段代码。

[todo 配图：scroller静止手指触碰wrapper]

当手指触碰到wrapper时，触发了touchstart事件，进而触发touchstart的监听函数_start。首先，我们需要拿到这个初始点很重要的信息--坐标，这其实是很容易拿到的，就是代码中的 e.touches[0]，然后保存在point中。touches是一个数组，当我们用两个指头去触碰wrapper时，我们可以从e.touches[1]中拿到另一个触碰点的信息。事实上，现阶段的所有zoom功能，都是基于这个最基本的原理展开的。point中的pageX和pageY记录了这个初始点的坐标。在IScroll中，分别用this.pointX和this.pointY记录了初始点的坐标信息。
this.moved表示手指在wrapper上是否发生了有效移动。在touchstart事件发生是，显然手指在wrapper上是没有移动的，因此在_start方法中将moved设为false。

[todo 配图：scroller运动时手指触碰wrapper]

我们似乎有这样一个设定，就是当手指接触到wrapper时，scroller一定处于静止状态。这当然是不全面的，因为scroller完全可以还处在滚动状态。回忆一下你在iphone上浏览手机联系人列表，当你稍快速滑动时，列表仿佛获得了一个初速度，会自己开始做减速滚动。这个过程中，如果你的手指又放在屏幕上时，你会发现这个列表立刻停止了滚动，留在了当前的位置。

IScroll当然考虑到了这个情况。并且IScroll将“手指放在滚动的列表时，列表就立刻停止滚动”这个过程仔细拆解，翻译成可以用javascript表达的流程。

1. scroller还在滚动过程中 => this.isInTransition === true
2. 手指接触wrapper => _start
3. 获得scroller当前的滚动位置pos  => this.getComputedPosition()
4. 立刻停止scroller正在进行的滚动 => this._transitionTime();this.isInTransition = false;
5. 将scroller立刻滚动到pos位置 => this._translate(pos.x,pos.y)

现在，你应该能理解这段代码了。
this._transitionTime();
if ( this.isInTransition ) {
	this.isInTransition = false;
	pos = this.getComputedPosition();
	this._translate(Math.round(pos.x), Math.round(pos.y));
}




