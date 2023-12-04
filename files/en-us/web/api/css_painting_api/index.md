---
title: CSS Painting API
slug: Web/API/CSS_Painting_API
page-type: web-api-overview
status:
  - experimental
browser-compat: api.PaintWorkletGlobalScope
---

{{DefaultAPISidebar("CSS Painting API")}}{{SeeCompatTable}}

CSS Painting API 是 [CSS Houdini](/zh-CN/docs/Web/Guide/Houdini) API 系列的一部分，允许开发人员编写可以直接绘制元素背景、边框或内容的JavaScript函数。

## 概念及用法

本质上来说， CSS Painting API 包含允许开发人员为 {{cssxref('image/paint', 'paint()')}} （ CSS [`<image>`](/zh-CN/docs/Web/CSS/image) 函数）创建自定义值的功能，然后，您可以将这些值应用于诸如 {{cssxref("background-image")}} 之类的属性，以在元素上设置复杂的自定义背景。
例如:

```css
aside {
  background-image: paint(myPaintedImage);
}
```

这个API定义了一个 {{domxref('worklet')}} ，可用于以编程方式生成一个图像，并在计算样式发生变化时做出响应。如果你想深入了解如何使用这个功能，建议查阅 [Using the CSS Painting API](/en-US/docs/Web/API/CSS_Painting_API/Guide).

## 接口

- {{domxref('PaintWorkletGlobalScope')}}
  - : 绘制工作线程的全局执行上下文。
- {{domxref('PaintRenderingContext2D')}}
  - : 实现 [`CanvasRenderingContext2D`](/en-US/docs/Web/API/CanvasRenderingContext2D) API 的一个子集。它具有一个输出位图，该位图的大小与它所渲染的对象的大小相同。
- {{domxref('PaintSize')}}
  - : 返回输出位图的宽度和高度的只读值。

## 示例

下面的例子创建了一个包含项目列表的页面，每个项目都有一个背景图像，而这个图像会在三种不同颜色和三种不同宽度之间轮换。在支持这些效果的浏览器中，你将看到类似于下方图片的效果。

![The width and color of the background image changes based on the custom properties](guide/boxbg.png)

To achieve this we'll define two custom CSS properties, `--boxColor` and `--widthSubtractor`.

### 绘制工作线程

在我们的工作台中，我们可以引用这些自定义属性。

```js
registerPaint(
  "boxbg",
  class {
    static get contextOptions() {
      return { alpha: true };
    }

    /*
     使用此函数检索任何用户自定义属性（或常规属性，如'height'）
     为元素定义的属性，将它们返回到指定的数组中
  */
    static get inputProperties() {
      return ["--boxColor", "--widthSubtractor"];
    }

    paint(ctx, size, props) {
      /*
       ctx -> 绘制上下文
       size -> 绘制尺寸：宽度和高度
       props -> 属性：get() 方法
    */

      ctx.fillStyle = props.get("--boxColor");
      ctx.fillRect(
        0,
        size.height / 3,
        size.width * 0.4 - props.get("--widthSubtractor"),
        size.height * 0.6,
      );
    }
  },
);
```

我们在 `registerPaint()` 类中使用了 `inputProperties()` 方法，获取了应用到 `boxbg` 元素上的两个自定义属性的值，然后在我们的 `paint()` 函数中使用了这些值。 `inputProperties()` 方法可以返回影响元素的所有属性，而不仅仅是自定义属性。

### 使用绘制工作线程

#### HTML

```html
<ul>
  <li>item 1</li>
  <li>item 2</li>
  <li>item 3</li>
  <li>item 4</li>
  <li>item 5</li>
  <li>item 6</li>
  <li>item 7</li>
  <li>item 8</li>
  <li>item 9</li>
  <li>item 10</li>
  <li>item 11</li>
  <li>item 12</li>
  <li>item 13</li>
  <li>item 14</li>
  <li>item 15</li>
  <li>item 16</li>
  <li>item 17</li>
  <li>item</li>
</ul>
```

#### CSS

在我们的css中，我们定义了 `--boxColor` 和 `--widthSubtractor` 自定义属性。

```css
li {
  background-image: paint(boxbg);
  --boxColor: hsl(55 90% 60% / 1);
}

li:nth-of-type(3n) {
  --boxColor: hsl(155 90% 60% / 1);
  --widthSubtractor: 20;
}

li:nth-of-type(3n + 1) {
  --boxColor: hsl(255 90% 60% / 1);
  --widthSubtractor: 40;
}
```

#### JavaScript

在我们的 `<script>` 中注册工作线程：

```js
CSS.paintWorklet.addModule("boxbg.js");
```

#### 结果

虽然你无法直接修改工作线程的脚本，但你可以在开发者工具中更改自定义属性的值，从而改变背景图像的颜色和宽度。

{{EmbedGHLiveSample("css-examples/houdini/css_painting_api/example-boxbg.html", '100%', 400)}}

## 规范

{{Specifications}}

## 浏览器兼容性

{{Compat}}

## 参见

- [Using the CSS Painting API](/en-US/docs/Web/API/CSS_Painting_API/Guide)
- [CSS Typed Object Model API](/en-US/docs/Web/API/CSS_Typed_OM_API)
- [CSS Houdini](/zh-CN/docs/Web/Guide/Houdini)
