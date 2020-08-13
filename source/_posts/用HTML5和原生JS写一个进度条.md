---
title: 用HTML5和原生JS写一个进度条
date: 2020-08-13 21:47:39
tags:
categories:
---

HTML5 对 web 表单有不少更新，比如为[`<input>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input)新增了<code>range</code>、<code>date</code>等`type`属性值。利用<code>range</code>这个`type`可以很简单地完成一个进度条组件，比用 CSS 写要简洁很多。

<!-- more -->

主要思路：

1.  `<input type="range" id="progress"/>`元素是进度条主体，通过`value`值控制进度，只要改变`value`即可实现对进度条的控制。
2.  `<input type="number" id="value"/>`元素获取进度条的`value`来显示具体的进度条值。通过互相监听`input`元素的`"change"`事件实现与进度条主体的数据绑定。
3.  给左右两侧的加减号监听鼠标`click`事件，使进度条和百分比值随之更新。
4.  给进度条增加`"mousemove"`事件监听赋值给`<input type="number"/>`元素，使拖动过程中保持数值的改变。

实现效果如下：

<pre>
  <style>
    #canvas {
      width: 400px;
      height: 200px;
      margin: 10% auto;
    }

    #progress-bar {
      display: flex;
      align-items: center;
      justify-content: center;
    }

    #value {
      border: none;
      width: 2.2em;
    }

    #value::-webkit-outer-spin-button,
    #value::-webkit-inner-spin-button {
      -webkit-appearance: none !important;
    }

  </style>

<body>
  <div id="canvas">
    <div id="progress-bar">
      <svg id="minus" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg" width="14" height="14">
        <path
          d="M911.232 420.565333l0 109.717333q0 22.848-16 38.848t-38.848 16l-694.848 0q-22.848 0-38.848-16t-16-38.848l0-109.717333q0-22.848 16-38.848t38.848-16l694.848 0q22.848 0 38.848 16t16 38.848z">
        </path>
      </svg>
      <input type="range" id="progress" min="0" max="100" step="0.1" value="50.0" />
      <svg id="plus" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg" width="14" height="14">
        <path
          d="M911.232 420.565333l0 109.717333q0 22.848-16 38.848t-38.848 16l-237.717333 0 0 237.717333q0 22.848-16 38.848t-38.848 16l-109.717333 0q-22.848 0-38.848-16t-16-38.848l0-237.717333-237.717333 0q-22.848 0-38.848-16t-16-38.848l0-109.717333q0-22.848 16-38.848t38.848-16l237.717333 0 0-237.717333q0-22.848 16-38.848t38.848-16l109.717333 0q22.848 0 38.848 16t16 38.848l0 237.717333 237.717333 0q22.848 0 38.848 16t16 38.848z">
        </path>
      </svg>
      <input type="number" id="value" min="0" max="100" step="0.1" value="50.0">%</div>
  </div>
  </div>
  <script>
    const progress = document.getElementById("progress");
    const minus = document.getElementById("minus");
    const plus = document.getElementById("plus");
    const value = document.getElementById("value");

    minus.addEventListener("click", () => {
      progress.value = Number(progress.value) - 10;
      value.value = progress.value;
    });
    plus.addEventListener("click", () => {
      progress.value = Number(progress.value) + 10; //注意有坑！value的值是string类型
      value.value = progress.value;
    });
    // 拖动进度条
    progress.addEventListener("mousemove", () => {
      value.value = Number(progress.value);
    });
    // 监听进度条改变
    progress.addEventListener("change", () => {
      value.value = progress.value;
    });
    value.addEventListener("change", () => {
      if (!value.value) {// 输入为空
        value.value = 0;
      }
      if (Number(value.value) >= 100) {// 输入大于100
        value.value = 100
      }
      value.value = Number(value.value)
      progress.value = value.value;
    });

  </script>
</body>
</pre>

完整代码如下：

```html
<!DOCTYPE html>
<html lang="zh-cn">

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>进度条</title>
  <style>
    #canvas {
      width: 400px;
      height: 200px;
      margin: 10% auto;
    }

    #progress-bar {
      display: flex;
      align-items: center;
      justify-content: center;
    }

    #value {
      border: none;
      width: 2.2em;
    }

    #value::-webkit-outer-spin-button,
    #value::-webkit-inner-spin-button {
      -webkit-appearance: none !important;
    }
  </style>
</head>

<body>
  <div id="canvas">
    <div id="progress-bar">
      <svg id="minus" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg" width="14" height="14">
        <path
          d="M911.232 420.565333l0 109.717333q0 22.848-16 38.848t-38.848 16l-694.848 0q-22.848 0-38.848-16t-16-38.848l0-109.717333q0-22.848 16-38.848t38.848-16l694.848 0q22.848 0 38.848 16t16 38.848z">
        </path>
      </svg>
      <input type="range" id="progress" min="0" max="100" step="0.1" value="50.0" />
      <svg id="plus" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg" width="14" height="14">
        <path
          d="M911.232 420.565333l0 109.717333q0 22.848-16 38.848t-38.848 16l-237.717333 0 0 237.717333q0 22.848-16 38.848t-38.848 16l-109.717333 0q-22.848 0-38.848-16t-16-38.848l0-237.717333-237.717333 0q-22.848 0-38.848-16t-16-38.848l0-109.717333q0-22.848 16-38.848t38.848-16l237.717333 0 0-237.717333q0-22.848 16-38.848t38.848-16l109.717333 0q22.848 0 38.848 16t16 38.848l0 237.717333 237.717333 0q22.848 0 38.848 16t16 38.848z">
        </path>
      </svg>
      <input type="number" id="value" min="0" max="100" step="0.1" value="50.0">%</div>
  </div>
  </div>
  <script>
    // 获取DOM元素
    const progress = document.getElementById("progress");
    const minus = document.getElementById("minus");
    const plus = document.getElementById("plus");
    const value = document.getElementById("value");

    // 监听"-"上的鼠标点击事件
    minus.addEventListener("click", () => {
      progress.value = Number(progress.value) - 10;
      value.value = progress.value;
    });
    plus.addEventListener("click", () => {
      progress.value = Number(progress.value) + 10; // 注意有坑！value的值是string类型
      value.value = progress.value;
    });
    // 监听拖动进度条
    progress.addEventListener("mousemove", () => {
      value.value = Number(progress.value);
    });
    // 监听进度条改变
    progress.addEventListener("change", () => {
      value.value = progress.value;
    });
    // 监听数值框输入
    value.addEventListener("change", () => {
      if (!value.value) {// 当输入为空的情况
        value.value = 0;
      }
      if (Number(value.value) >= 100) {// 输入大于100
        value.value = 100
      }
      value.value = Number(value.value)
      progress.value = value.value;
    });
  </script>
</body>

</html>
```
