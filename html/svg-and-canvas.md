# SVG & Canvas

## 一、引言

现在的前端业务中数据可视化的需求越来越多，大家常用的类库一个是echarts；另外一个就是D3.js 。两者看着都很酷炫，但依赖的底层是不一样的。echarts基于canvas，而D3.js以SVG为主。&#x20;

[Canvas](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FHTML%2FElement%2Fcanvas) 和 [SVG](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FGlossary%2FSVG) 都允许您在浏览器中创建图形，但是它们在根本上是不同的。

## **二、SVG**

SVG 意为可缩放矢量图形（Scalable Vector Graphics）是一种基于 [XML](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FGlossary%2FXML) 的图像格式，用于描述二维矢量图形的一种图形格式。SVG 是 W3C 制定的一种新的二维矢量图形格式，也是规范中的网络矢量图形标准。SVG 严格遵从 XML 语法，并用文本格式的描述性语言来描述图像内容，因此是一种和图像分辨率无关的矢量图形格式。

SVG 还有以下特点：

* SVG 使用 XML 描述的 2D 图像。
* SVG 是基于 xml 的，所以 svg 中绘制的图形和使用的元素，js 都可以给其添加事件。
* SVG 绘制的图像是一个对象，如果对象的属性发生改变，浏览器将重新绘制图形。

```html
<!-- SVG 是矢量和声明性的 -->
<svg viewBox="0 0 200 200">
    <circle cx="10" cy="10" r="10" />
</svg>
```

## **三、Canvas**

Canvas 是 HTML5 新增的，一个可以使用脚本在其中绘制图像的 HTML 元素。 ​Canvas 是由 HTML 代码配合高度和宽度属性而定义出的可绘制区域。JavaScript 代码可以访问该区域，类似于其他通用的二维 API，通过一套完整的绘图函数来动态生成图形。Canvas 会创建一个固定大小的画布，会公开一个或多个渲染上下文(画笔)，使用渲染上下文来绘制和处理要展示的内容。

Canvas 还有以下特点：

* 依赖分辨率
* 不支持事件处理器
* 弱的文本渲染能力
* 能够以 .png 或 .jpg 格式保存结果图像
* 最适合图像密集型的游戏，其中的许多对象会被频繁重绘

```html
<canvas id="myCanvas" width="578" height="200"></canvas>
<script>
  var canvas = document.getElementById('myCanvas');
  var context = canvas.getContext('2d');
  var centerX = canvas.width / 2;
  var centerY = canvas.height / 2;
  var radius = 70;

  context.beginPath();
  context.arc(centerX, centerY, radius, 0, 2 * Math.PI, false);
  context.fillStyle = 'green';
  context.fill();
</script>
```

## **四、区别**

| Canvas                    | SVG                            |
| ------------------------- | ------------------------------ |
| 基于栅格（由像素组成）               | 基于矢量（由形状组成），非常适合 UI/UX 动画      |
| 依赖分辨率                     | 不依赖分辨率                         |
| 不支持事件处理器                  | 支持事件处理器                        |
| 文本渲染能力差                   | 良好的文字渲染功能                      |
| 使用更多的对象或更小的曲面或两者都提供更好的性能  | 复杂度高会减慢渲染速度（任何过度使用 DOM 的应用都不快） |
| 最适合图像密集型的游戏，其中的许多对象会被频繁重绘 | 不适合游戏应用                        |
| 仅通过脚本修改                   | 通过脚本和 CSS 修改                   |
| 能够以 .png 或 .jpg 格式保存结果图像  | 多个图形元素，成为页面 DOM 树的一部分          |
| 可伸缩性差。不适合以较高分辨率打印。可能发生像素化 | 更好的可扩展性。可以任何分辨率高质量打印。不会发生像素化   |

## 五、应用

* Canvas 适用于位图，高数据量高绘制频率（帧率）的场景，如动画、游戏；&#x20;
* SVG 适用于矢量图，低数据量低绘制频率的场景，如图形、图表；

[https://raw.githubusercontent.com/abcrun/abcrun.github.com/master/images/canvas\_svg/canvas\_svg.jpg](https://raw.githubusercontent.com/abcrun/abcrun.github.com/master/images/canvas\_svg/canvas\_svg.jpg)

## 六、参考

* [https://juejin.cn/post/7057410984914190350](https://juejin.cn/post/7057410984914190350)
* [https://juejin.cn/post/6998102800932536356](https://juejin.cn/post/6998102800932536356)
* [https://www.jianshu.com/p/8ab26a310359](https://www.jianshu.com/p/8ab26a310359)
