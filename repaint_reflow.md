> 页面的呈现流程

1. 浏览器将获取到的 html 文档解析成一个 Dom 树，html 中的每个元素都是 Dom 树中的一个节点，根节点就是 document。Dom 树中包含所有的 html 标签，包括 display:none，以及 js 动态添加的元素。
2. 浏览器将所有的 CSS 解析成样式结构，在解析过程中去掉浏览器不能识别的样式，比如 IE 去掉-moz-等开头的样式，Firefox 去掉\_等开头的样式。
3. 浏览器将 Dom 树和 CSS 的样式结构体组合后生成 Render Tree。Render Tree 类似于 Dom Tree，但有很大的区别。Render Tree 能够识别样式，每个节点都有自己的样式，并且不包含隐藏节点（例如 display:none 和 head 等），但是 visibility:hidden 会包含在 Render Tree 中，因为 visibility:hidden 会影响页面的布局，会占空间。
4. Render Tree 构建完成之后，浏览器根据 Render Tree 开始绘制页面。

> 重绘和回流

回流：当 Render Tree 中的元素因为布局、尺寸、隐藏等改变需要重新构建，就称为回流。每个页面至少回流一次，就是在页面一次加载的时候。完成回流后，浏览器会重新绘制受影响的部分到屏幕中。

重绘：在页面中的元素需要更新一些背景等影响外观的样式，而不绘影响布局的属性时，就称为重绘。

注意：回流必将引起重绘，而重绘不一定会引起回流。

> 回流的原因

1. Dom 操作；
2. 调整窗口大小；
3. 元素位置或尺寸改变——边距、填充、边框、宽度和高度；
4. 字体大小改变；
5. 页面渲染的初始化
6. 元素内容变化，尤其是输入控件；
7. 请求了 getComputedStyle(), 或者 IE 的 currentStyle，或 offsetWidth, width, clientWidth, scrollTop/scrollHeight 的计算。

> 减少回流、重绘

1. 避免逐项改变样式，最好一次性改变样式属性。或者将样式定义成 class 进行一次性修改。
2. 避免直接操作 Dom，创建一个 documentFragment 或 div，在它上面进行 Dom 操作，只引发一到俩次回流和重绘。也可以使用 cloneNode(true or false) 和 replaceChild 技术，只引发一次回流和重绘。
3. 避免循环读取 offsetTop 等属性。
4. 绝对定位具有复杂动画的元素。绝对定位使它脱离文档刘，否则会引起父元素及后续元素大量的回流。

> 浏览器的优化

回流会增加很多的开销，如果每句 Js 都进行一次回流和重绘的话，浏览器会受不了，所以很多浏览器都会优化这些操作。浏览器会维护一个队列，所有的回流和重绘放入到这个队列中，等队列到达一定的数量或到一定的时间间隔，浏览器就会 flush 队列，将队列中的操作进行一次批处理，这样就将一个多次回流和重绘合并成了一次。

虽然有了浏览器的优化，但有时候我们写的一些代码可能会强制浏览器提前 flush 队列，这样浏览器的优化可能就起不到作用了。当你请求向浏览器请求一些 style 信息的时候，就会让浏览器 flush 队列，比如：

1. offsetTop, offsetLeft, offsetWidth, offsetHeight
2. scrollTop/Left/Width/Height
3. clientTop/Left/Width/Height
4. width,height
5. 请求了 getComputedStyle(), 或者 IE 的 currentStyle

当你请求上面的一些属性的时候，浏览器为了给你最精确的值，需要 flush 队列，因为队列中可能会有影响到这些值的操作。即使你获取元素的布局和样式信息跟最近发生或改变的布局信息无关，浏览器都会强行刷新渲染队列。
