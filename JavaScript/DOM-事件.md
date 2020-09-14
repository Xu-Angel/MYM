# 事件

[DOM-Level-3-Events](https://www.w3.org/TR/DOM-Level-3-Events/)

[Event](https://developer.mozilla.org/zh-CN/docs/Web/API/Event)

浏览器的事件模型【事件驱动编程模式（event-driven）】，就是通过监听函数（listener）对事件做出反应。事件发生后，浏览器监听到了这个事件，就会执行对应的监听函数。

在DOM中，由用户处触发的、API生成的、脚本触发的、自定义的事件都归类于DOM-事件中。

**分类：**

- HTML 的 on- 属性
  ```js
  //
  <body onload="doSomething()">
  -----------------
  el.setAttribute('onclick', 'doSomething()');// <Element onclick="doSomething()">
  ```
  违反了 HTML 与 JavaScript 代码相分离的原则，将两者写在一起，不利于代码分工，因此不推荐使用。

- 元素节点的事件属性
  ```js
  window.onload = doSomething;

  div.onclick = function (event) {
    console.log('触发事件');
  };
  ```
  缺点在于，同一个事件只能定义一个监听函数，也就是说，如果定义两次onclick属性，后一次定义会覆盖前一次。因此，也不推荐使用。

- EventTarget.addEventListener是推荐的指定监听函数的方法。它有如下优点：

  - 同一个事件可以添加多个监听函数。
  - 能够指定在哪个阶段（捕获阶段还是冒泡阶段）触发监听函数。
  - 除了 DOM 节点，其他对象（比如window、XMLHttpRequest等）也有这个接口，它等于是整个 JavaScript 统一的监听函数接口。

## 事件流定义

[event-flow](https://www.w3.org/TR/DOM-Level-3-Events/#event-flow)

DOM结构是一个树型结构，当一个HTML元素触发一个事件时，该事件会在元素结点与根结点之间的路径传播，路径所经过的结点都会收到该事件，这个传播过程可称为 DOM 事件流（DOM event flow ）。

传播（Propagation）按顺序分三个阶段（Any event taking place in the W3C event model is first captured until it reaches the target element and then bubbles up again）：

- 捕获阶段（capture phrase，从根节点window到目标节点，即最近的、最精确的元素节点）
- 目标阶段（target phrase，目标节点上的事件触发按代码执行顺序触发）
- 冒泡阶段（bubbling phrase，从目标节点到根节点 ）

*[以往事件流顺序兼容问题](https://www.quirksmode.org/js/events_order.html#link4)*

## EventTarget接口

[EventTarget](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget)

[eventtarget](https://wangdoc.com/javascript/events/eventtarget.html)

### addEventListener

[addEventListener](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)

**注意点：三个参数的不同**

Grammer

```js
target.addEventListener(type, listener, options);
target.addEventListener(type, listener, useCapture);
target.addEventListener(type, listener, useCapture, wantsUntrusted  );  // Gecko/Mozilla only
```

## Event对象

[Event](https://developer.mozilla.org/zh-CN/docs/Web/API/Event)

[event](https://wangdoc.com/javascript/events/event.html)

### 实例属性

挑几个重点

- Event.bubbles Event构造函数生成的事件，默认是不冒泡的。
- Event.eventPhase 返回值
  - 0，事件目前没有发生。
  - 1，事件目前处于捕获阶段，即处于从祖先节点向目标节点的传播过程中。
  - 2，事件到达目标节点，即Event.target属性指向的那个节点。
  - 3，事件处于冒泡阶段，即处于从目标节点向祖先节点的反向传播过程中。

- Event.cancelable 多数浏览器的原生事件是可以取消的。比如，取消click事件，点击链接将无效。但是除非显式声明，**Event构造函数生成的事件，默认是不可以取消的。和Event.preventDefault()相互拮抗**

*事件发生以后，会经过捕获和冒泡两个阶段，依次通过多个 DOM 节点。因此，任意时点都有两个与事件相关的节点，一个是事件的原始触发节点（Event.target），另一个是事件当前正在通过的节点（Event.currentTarget）。前者通常是后者的后代节点。*

**Q:e.currentTarget总是等同于监听函数内部的this吗?**

- Event.currentTarget属性返回事件当前所在的节点，即事件当前正在通过的节点，也就是当前正在执行的监听函数所在的那个节点。随着事件的传播，这个属性的值会变。
- Event.target属性返回原始触发事件的那个节点，即事件最初发生的节点。这个属性不会随着事件的传播而改变。


- Event.isTrusted 属性返回一个布尔值，表示该事件是否由真实的用户行为产生

- Event.detail 属性只有浏览器的 UI （用户界面）事件才具有。该属性返回一个数值，表示事件的某种信息。具体含义与事件类型相关。比如，对于click和dblclick事件，Event.detail是鼠标按下的次数（1表示单击，2表示双击，3表示三击）；对于鼠标滚轮事件，Event.detail是滚轮正向滚动的距离，负值就是负向滚动的距离，返回值总是3的倍数。

### stopPropagation

stopPropagation方法阻止事件在 DOM 中继续传播，防止再触发定义在别的节点上的监听函数，但是不包括在当前节点上其他的事件监听函数。

[stopPropagation](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/stopPropagation)方法

```js
// 事件传播到 p 元素后，不再向下传播[捕获阶段]
p.addEventListener('click', function (event) {
  event.stopPropagation();
}, true);

// 事件冒泡到 p 元素后，不再向上冒泡[冒泡阶段]
p.addEventListener('click', function (event) {
  event.stopPropagation();
}, false);
--------------------------------
function stopEvent(e) {
  e.stopPropagation();
}

el.addEventListener('click', stopEvent, false);//click事件将不会进一步冒泡到el节点的父节点。

```

### stopImmediatePropagation

[stopImmediatePropagation](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/stopImmediatePropagation)

Event.stopImmediatePropagation方法阻止同一个事件的其他监听函数被调用，不管监听函数定义在当前节点还是其他节点。该方法阻止事件的传播，比Event.stopPropagation()更彻底。

如果同一个节点对于同一个事件指定了多个监听函数，这些函数会根据添加的顺序依次调用。**只要其中有一个监听函数调用了Event.stopImmediatePropagation方法，其他的监听函数就不会再执行了**。

```js
function l1(e){
  e.stopImmediatePropagation();
}

function l2(e){
  console.log('hello world');
}

el.addEventListener('click', l1, false);// 阻止click事件冒泡,并且阻止p元素上绑定的其他click事件的事件监听函数的执行.所以l2不会被调用。
el.addEventListener('click', l2, false);
```

### composedPath

[composedPath](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/composedPath)

Event.composedPath()返回一个数组，成员是事件的最底层节点和依次冒泡经过的所有上层节点。

```js
// HTML 代码如下
// <div>
//   <p>Hello</p>
// </div>
var div = document.querySelector('div');
var p = document.querySelector('p');

div.addEventListener('click', function (e) {
  console.log(e.composedPath());
}, false);
// [p, div, body, html, document, Window]
```
## 常用事件

鼠标事件
键盘事件
表单事件
触摸事件
拖拉事件
进度事件
错误事件

>quote:Javascript权威指南与高级编程

```
(1) UI(用户界面)事件：当用户与页面上的元素交互时发生
    1) load
        当页面完全加载后(包括所有的图像、JS文件、CSS文件等)
    2) unload
        当页面完全卸载后触发，只要用户从一个页面切换到另外一个页面，就会发生，
        此事件大多用于清除引用，以避免内存泄露
    3) resize
    4) scroll
(3) 焦点事件
    1) blur:不会冒泡，所有浏览器支持
    2) focus:不会冒泡，所有浏览器支持
    3) focusin:与focus等价，但是会冒泡，IE5+
    4) focusout:与blur等价，IE5+
(4) 鼠标事件
    1) click
    2) dblclick
    3) mousedown
    4) mouseenter:不冒泡
    5) mouseleave:不冒泡
    6) mousemove：当鼠标在元素内部移动时重复触发
    7) mouseout
    8) mouseover
    9) mouseup
    以上事件，除了mouseenter与mouseleave之外，其余都会冒泡
(5) 滚轮事件
(6) 文本事件：用户输入文本
(7) 键盘事件
(8) 合成事件：当输入法编辑器输入字符时触发
(9) 变动事件：当底层DOM结构变化时触发
(10) 变动名称事件：已废弃
```
## 应用-事件代理

由于事件会在冒泡阶段向上传播到父节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件。这种方法叫做事件的代理（delegation）

```js
var ul = document.querySelector('ul');

ul.addEventListener('click', function (event) {
  if (event.target.tagName.toLowerCase() === 'li') {
    // some code
  }
});
```

### QA

- 编写一个通用的事件监听函数
- 简述事件流
- 对于一个无限下拉加载图片的页面，如何给每个图片绑定事件
- 谈谈事件委托（代理）

  事件委托， 现实意义上讲是指将自己的事务嘱托他人代为处理。js中是 允许我们不必为某些特定的节点添加事件监听器，而是将事件监听器添加到（这些节点的）某个 parent节点上。利用事件冒泡，去找到匹配的子节点元素，然后做出相应的事件响应。是主要用来解决“事件处理程序过多”这个问题的。
  经典例子：ul 里面的li的事件绑定。
  - 好处：
    1. 简化了初始化的过程，减少了多余的事件处理函数，进而节省了内存。提高性能。
    2. 新添加的元素还会有之前的事件。

  - 缺点：

    1. 要求事件在IE中必须冒泡. 大多数的事件会冒泡，但是并不是所有的。对于其他的浏览器而言，捕获阶段也会同样适用。
    2. 理论上委托会导致浏览器额外的加载，因为在容器内的任意一个地方事件的发生，都会运行事件处理函数，所以多数情况下事件处理函数都是在空循环（没有意义的动作），通常不是什么大不了的事儿。
    3. 如果现在的dom 元素分为很多很多层，对于底层事件的委托，有可能在事件冒泡的过程中，中途被某个节点 终止冒泡了，这样事件就传递不到上层，则委托就会失败了。
- 谈谈事件流的兼容问题