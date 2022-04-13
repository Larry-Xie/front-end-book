# CSS 定位和浮动

## 一、引言

定位是一个相当复杂的话题，在去深入理解定位之前，我们先来聊一下之前我们的标准文档流下的布局。

首先，

* 盒模型，元素的内容宽高、padding、border 以及 margin；
* 元素的分类：块级元素、行内元素、行内块元素之间的区别；
* 盒模型的 margin，垂直方向上会出现外边距合并的问题；

好了，定位的整个想法是允许我们**覆盖**上述描述的基本标准文档流的行为，以产生有趣的效果。

## 二、定位属性

有许多不同类型的定位，你可以对 HTML 元素产生效果，要想使某个元素上进行定位，我们使用position属性。

css **position** 属性用于指定一个元素在文档中的定位方式。`top`，`right`，`bottom`，`left`属性则决定了该元素的最终位置。

| 属性值      | 描述                                                                                                 |
| -------- | -------------------------------------------------------------------------------------------------- |
| static   | **默认。静态定位**， 指定元素使用正常的布局行为，即元素在文档常规流中当前的布局位置。此时 `top`, `right`, `bottom`, `left` 和 `z-index` 属性无效。 |
| relative | **相对定位**。 元素先放置在未添加定位时的位置，在不改变页面布局的前提下调整元素位置（因此会在此元素未添加定位时所在位置留下空白）                                |
| absolute | **绝对定位**。不为元素预留空间，通过指定元素相对于最近的非 static 定位祖先元素的偏移，来确定元素位置。绝对定位的元素可以设置外边距（margins），且不会与其他边距合并        |
| fixed    | **固定定位**。 不为元素预留空间，而是通过指定元素相对于屏幕视口（viewport）的位置来指定元素位置。元素的位置在屏幕滚动时不会改变                             |
| sticky   | **粘性定位**。粘性定位可以被认为是相对定位和固定定位的混合。元素在跨越特定阈值前为相对定位，之后为固定定位。                                           |

## 三、静态定位

静态定位意味着“元素默认显示文档流的位置”。没有任何变化。

代码演示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>静态定位</title>
	<style type="text/css">
		.positioned{
			position: static;
			background-color: red;
		}
	</style>
</head>
<body>
	<p class="positioned">我是静态定位的元素</p>
	
</body>
</html>
```

效果显示：

![静态定位](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c37326e3f1d34d118f12e0aaafe729d5\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 四、相对定位

相对定位的元素是在文档中的正常位置的偏移，但是不会影响其他元素的偏移。

举个例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>相对定位</title>
	<style type="text/css">
		div{
			width: 200px;
			height: 300px;
		}
		.box1{
			background-color: red;
		}
		.box2{
			position: relative;
			top: 30px;
			left: 40px;
			background-color: green;
		}
		.box3{
			background-color: blue;
		}
	</style>
</head>
<body>
	<div class="box1">盒子1</div>
	<div class="box2">盒子2</div>
	<div class="box3">盒子3</div>
</body>
</html>
```

效果展示：

![相对定位](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e66d82986cc24a86a20e2607e9ba7b51\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

咦，很酷是嘛？好吧这个结果可能不是你所期待的———为什么它移动到底部和右边呢，我们不是指定顶部和左边么？听起来不合逻辑，但这只是相对定位工作的方式———你需要考虑一个看不见的力，推动定位的盒子的一侧，移动它相反的方向。所以，如果您制定了`top:30px;`一个里推动框的顶部，使它向下移动 30px。

其实说白了，我们得找一个参考的位置，然后进行定位，那么相对定位是以自身原来的位置进行定位的。

#### **参考点**

以自身原来的位置进行定位，可以使用`top, left, right, bottom` 对元素进行偏移；

#### **现象**

1. 不脱离标准文档流，单独设置盒子相对定位之后，如果不用`top, left, right, bottom` 对元素进行偏移，那么与普通的盒子没什么区别；
2. 有压盖现象。用`top, left, right, bottom`对元素进行偏移之后，明显定位的元素的层级高于没有定位的元素；

#### **应用**

相对定位的盒子，一般用于`子绝父相`布局模式的参考。

## 五、绝对定位

相对定位的元素并没有脱离标准文档流，而绝对定位的元素则脱离了文档流。在标准文档流中，如果一个盒子设置了绝对定位，那么该元素不占据空间。并且绝对定位元素相对于最近的非 static 祖先元素定位。当这样的祖先元素不存在时，则相对于根元素页面的左上角进行定位。

举个栗子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>绝对定位</title>
	<style type="text/css">
		div{
			width: 200px;
			height: 300px;
			border: 3px solid #ff6700;
		}
		.box1{
			position: absolute;
			left: 50px;
			background-color: red;
		}
		.box2{
			background-color: green;
		}
		.box3{
			background-color: blue;
		}
	</style>
</head>
<body>
	<div class="box1">One</div>
	<div class="box2">Two</div>
	<div class="box3">Three</div>
</body>
</html>
```

效果展示：

![绝对定位](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/56297cdb81524ce384a852e504668c08\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

绝对定位元素相对于最近的非static祖先元素定位，这句话怎么理解呢？举个栗子吧

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>相对定位</title>
	<style type="text/css">
		body{
			border: 1px solid #000;
		}
		.grandfather{
			position: relative;
			border: 3px solid #237D7C;
		}
		.father{
			position: relative;
			top: 50px;
			left: 50px;
			border: 3px solid purple;
		}
		.box1,.box2,.box3{
			width: 200px;
			height: 300px;
			border: 3px solid #ff6700;
		}
		.box1{
			position: absolute;
			left: 50px;
			background-color: red;
		}
		.box2{
			background-color: green;
		}
		.box3{
			background-color: blue;
		}
	</style>
</head>
<body>
	<div class="grandfather">
		<div class="father">
			<div class="box1">One</div>
			<div class="box2">Two</div>
			<div class="box3">Three</div>
		</div>
	</div>
</body>
</html>
```

效果显示：

![绝对定位](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/51c3994310174e9493f0af5c861b7c89\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

绝对定位的盒子是以最近的非 static 定位的父元素进行定位，为了演示此效果。特意将网页结构层次增多，并且区分跟 body 页面左上角的位置，将紫色边框的父元素进行了位置调整，并且将`.father`和`.grandfather`元素同时设置了相对定位。来完成此效果。

#### **参考点**

相对于最近的非 static 祖先元素定位，如果没有非 static 祖先元素，那么以页面左上角进行定位。

#### **现象**

1. 脱离了标准文档流，不在页面中占位置
2. 层级提高，做网页压盖效果

#### **应用**

网页中常见的布局方案：`子绝父相`。

> 注意：子绝父绝，子绝父固，都是以最近的非 static 父辈元素作为参考点。父绝子绝，子绝父固，没有实战意义，布局网站的时候不会出现父绝子绝。
>
> 因为绝对定位脱离标准流，影响页面的布局。
>
> 相反`父相子绝`在我们页面布局中，是常用的布局方案。因为父亲设置相对定位，不脱离标准流，子元素设置绝对定位，仅仅的是在当前父辈元素内调整该元素的位置。

## 六、固定定位

它跟绝对定位基本相似，只有一个主要区别：绝对定位固定元素是相对于 html 根元素或其最近的定位祖先元素，而固定定位固定元素则是相对于浏览器视口本身。这意味着你可以创建固定的有用的网页效果，比如固定导航栏、回到顶部按钮，小广告等。

让我们举个例子来说明上面的意思。

**html:**

```html
<div class="outer">
    <p>
        某女买了一件1000块的衣服。我质疑有点贵。

        她说：“贵？我跟你说，这件衣服原价2000块，打了五折之后便宜一半，就等于我赚了1000块！虽然我花出去1000块，但同时我又赚回来了1000块，所以这件衣服相当于是白送，免费。你懂个屁！ ”

        我被她的经济数学头脑震惊得久久说不出话来……
    </p>
    <p>
        国家全面放开生育二胎的政策春风送暖，某机关一张姓女公务员前些天终于怀上二胎了，又一次升级为准妈妈。

        老公让她赶紧跟领导说一下，争取减点工作量，好好保胎。

        午饭时，她在食堂恰好碰见领导，难掩一脸的兴奋汇报：“头儿，我刚怀上小二了”！

        周围突然安静下来了。领导楞了半天回过神来，憋出了一句：“你老公知道了吗？”

        她脑抽地答了一句：“是他让我找你的”……

        食堂里瞬间鸦雀无声！
        此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！此处省略5000字！！
    </p>
    <div class="box" id="one">One</div>
</div>
```

**css:**

```css
.box {
    background: red;
    width: 100px;
    height: 100px;
    margin: 20px;
    color: white;
}
#one {
    position: fixed;
    top: 80px;
    left: 10px;
}
.outer {
    width: 500px;
    height: 300px;
    overflow: scroll;
    padding-left: 150px;
}
复制代码
```

效果显示：

![绝对定位](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cbfeaf29301346faaf7c72b0fa1c470f\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 七、粘性定位

sticky 定位结合了 relative 定位及 fixed 定位，主要用在对 scroll 事件的监听上，当元素距离其父元素的距离达到 sticky 定位要求时，sticky 定位相当于 fixed 定位，固定在某个位置，未达到定位要求前，其相当于 relative 定位。

当使用粘性定位时需要满足以下条件才能生效：

* 父元素不能 overflow:hidden 或者 overflow:auto 属性；
* 必须指定 top、bottom、left、right 4个值之一，否则只会处于相对定位；
* 父元素的高度不能低于 sticky 元素的高度；
* sticky 元素仅在其父元素内生效；

使用粘性定位时需要注意：

* sticky 不会触发 BFC；
* z-index 无效；
* 当父元素的 height：100% 时，页面滑动到一定高度之后 sticky 属性会失效；
* IE 低版本不支持 sticky 的使用；
* 兼容性较差；

&#x20;举个例子：

* 当鼠标下滑到一定高度时，触发 position:sticky 定位的要求，让“流行，新款，精选”固定为距离顶部44px 的地方。

![粘性定位](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/11/170c9c195a0a9254\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

* css：

```css
.tab-control{
  position: sticky;
  top: 44px;
}
```

* html

```html
<tab-control class="tab-control" :titles="['流行','新款','精选']"></tab-control>
```

## 八、参考

* [CSS的浮动和定位布局详细](https://juejin.cn/post/6886247611318140942)
* [CSS中容易被忽视的 position 属性 sticky](https://juejin.cn/post/6844904087486464007)
* [position:sticky解析](https://juejin.cn/post/6923866099981893639)\
