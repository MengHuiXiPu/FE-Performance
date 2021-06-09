## 如何提高CSS性能

结合现代网站的复杂性和浏览器处理[CSS](http://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=200264691&idx=2&sn=d85ccdaaf4a153ed40d7fa2bf7fab8ce&scene=21#wechat_redirect)的方式，即使是适量的CSS也会成为设备受限、网络延迟、带宽或数据限制的瓶颈。因为性能是用户体验的一个至关重要的部分，所以必须确保在各种形状和尺寸的设备上提供一致的高质量体验，这也需要优化你的CSS。

本篇文章将涵盖CSS会导致哪些性能问题，以及如何制作不妨碍人们[使用](http://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=200295686&idx=1&sn=c6a1766cca25f63556c1694e83a5d5d6&scene=21&subscene=126#wechat_redirect)的CSS的最佳实践。

#### 目录

- CSS是如何工作的？
- 注意CSS的大小
- 优先考虑关键的CSS
- 使用高效的CSS动画
- 使用CSS优化字体[加载](http://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=200091794&idx=1&sn=44ab88a2bae4bc38f0be0ef99e10b8f6&scene=21&subscene=126#wechat_redirect)
- 不用担心CSS选择器的速度问题。

#### CSS是如何工作的？

##### CSS阻止渲染

当一个[页面](http://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=200190940&idx=1&sn=c57c27fb8ff282606f050380dce920ba&scene=21&subscene=126#wechat_redirect)有CSS可用时，无论是内联还是外部样式表，浏览器都会延迟渲染，直到CSS被解析。这是因为没有CSS的页面通常是不可用的。如果浏览器向你展示了一个没有CSS的乱七八糟的页面，然后片刻后又啪啪啪地进入了一个有样式的页面，不断变化的内容和突如其来的视觉变化会让用户体验混乱。这种糟糕的用户体验有一个名字--Flash of Unstyled Content（FOUC）。



##### CSS可以阻止HTML的解析

尽管浏览器在完成CSS解析之前不会显示内容，但它会处理HTML的其余部分。然而脚本会阻止解析器，除非它们被标记为defer或async。一个脚本有可能操纵页面和其余代码，所以浏览器必须注意该脚本的执行时间。

![图片](https://mmbiz.qpic.cn/mmbiz_png/meG6Vo0MevjTHcJ93syGGic6YWsN8DooRDCvQz50W6MJ2QLbkhb2dibx8ChGg4VQtQDNfhQT46AFBTU7fE6zKEzQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

屏蔽脚本的解析器：脚本如何屏蔽HTML解析。

因为脚本可以影响应用到页面的样式，如果浏览器仍在处理一些CSS，它就会等到处理完毕再运行脚本。因为在脚本运行之前不会继续解析文档，这意味着CSS不再只是阻止渲染--取决于文档中外部样式表和脚本的顺序，也可能停止HTML解析。

![图片](https://mmbiz.qpic.cn/mmbiz_png/meG6Vo0MevjTHcJ93syGGic6YWsN8DooRAWvVib8IMPCS7AsNHz8Znq4DPriaerj0xq8fFb8XILeibdhibibSUsfwUwA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

解析器阻塞CSS：CSS如何阻塞HTML解析。

为了避免阻塞解析，请尽快交付CSS，并以最佳顺序安排你的资源。

#### 注意CSS的大小

##### 压缩和最小化CSS

建立连接来下载外部样式表不可避免地会造成延迟，但你可以通过最小化网络传输的总字节来加快下载速度。

压缩[文件](http://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=200279854&idx=1&sn=65e4e0881df476714358309bbd0ae477&scene=21&subscene=126#wechat_redirect)可以显著提高速度，许多托管平台和CDN都会在默认情况下对资产进行压缩编码（或者你可以轻松配置）。服务器和客户端交互中使用最广泛的压缩格式是Gzip。还有Brotli，它可以提供更好的压缩效果，尽管它不像 Gzip 那样受到支持。

最小化是去除空白和任何不必要的代码的过程。输出的是一个更小但完全有效的代码文件，浏览器可以解析，这将为你节省一些字节。Terser是一个流行的JavaScript压缩工具，如果你使用webpack，v4包含一个插件来创建minified的构建文件。

##### 微调：删除未使用的CSS

在使用CSS框架的时候，最终得到未使用的 CSS 是相对常见的（除非我们只包含我们需要的组件）。同样的问题也出现在长期增长的大型代码库中。

去除未使用的CSS通常是手工操作。主要的挑战在于它有多么复杂。我们必须在所有可能的状态下，在所有可能的设备上仔细审核整个网站（以覆盖媒体查询），并执行所有可能改变样式的JavaScript功能。UnusedCSS和PurifyCSS是流行的工具，可以帮助查明不必要的样式，但我们应该配合仔细的视觉回归测试。

在这里，使用CSS-in-JS的显著优势：每个组件内渲染的样式都是只需要CSS。在CSS-in-JS中加快CSS的秘诀是将CSS内联到页面中，或者将其提取到外部CSS文件中。将CSS发送到一个JavaScript文件中会导致它的解析和缓慢计算。

#### 优先考虑关键的CSS

关键的CSS是一种技术，它提取并内嵌CSS以获得页面以上的内容。在HTML文档的 `<head>`中内联提取的样式，无需额外请求获取这些样式，并加快渲染速度。

> 你知道吗？Above-the-fold是指浏览者在滚动之前在页面加载时看到的所有内容。由于有许多设备和屏幕尺寸，所以没有一个普遍定义的像素高度被认为是折叠以上的内容。

为了最大限度地减少首次渲染的往返次数，将上述内容保持在14KB（压缩）以下。

确定关键的CSS并不完全准确，因为你需要对折叠位置进行假设（不同设备屏幕尺寸的折叠位置有所不同）。这对于高度动态的网站来说是很困难的。即使不精确，它仍然可以带来性能的提升，我们可以通过Critical、CriticalCSS和Penthouse等工具自动化。

##### 异步加载CSS

CSS的其余部分（不太关键的部分）最好是异步加载。实现的方法是将link media属性设置为print。

```
<linkrel="stylesheet"href="non-critical.css"media="print"onload="this.media='all'">
```

"Print"媒体类型定义了用户试图打印页面时的样式表规则，浏览器将在不延迟页面渲染的情况下加载这种样式表。当样式表加载完成后，将该样式表应用于所有媒体（即屏幕而不仅仅是打印），使用onload属性将媒体设置为all。

另一种方法是使用 `<linkrel="preload">` (而不是rel="styleheet")来实现类似的模式，并在加载事件中切换rel属性到styleheet。在使用这种方法时，有一些缺点需要考虑。

- 浏览器对预加载的支持还不是很好，所以需要一个polyfill（或者使用loadCSS等库）来跨浏览器应用样式表。
- 预加载会很早地以最高优先级获取文件，可能会降低其他重要下载的优先级。

如果你确实想要预加载提供的高优先级获取（在支持它的浏览器中），loadCSS的创建者建议你把它和第一种模式结合起来，就像这样。

```
<linkrel="preload"href="/path/to/my.css"as="style"><linkrel="stylesheet"href="/path/to/my.css"media="print"onload="this.media='all'">
```

##### 避免在CSS文件中使用@import

在CSS文件中使用@import会降低渲染速度。首先，浏览器必须下载CSS文件来发现导入的资源，然后在渲染之前发起另一个请求来下载它。

如果你有一个包含@import url(import.css)的样式表；网络瀑布看起来像这样。

![图片](https://mmbiz.qpic.cn/mmbiz_png/meG6Vo0MevjTHcJ93syGGic6YWsN8DooRpmkP1ELzqDhFognakVZduAtesf6LUnhcjQjk1O9pyfQibp0kaTdo59A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在link元素中加载两个样式表，允许并行下载。

![图片](https://mmbiz.qpic.cn/mmbiz_png/meG6Vo0MevjTHcJ93syGGic6YWsN8DooRia3VCTRX6k9qd20zJbWGq8LMnicq1hibxqzx9jsR94Er1uJGicTKgO6CHg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### 使用高效的CSS动画

当你对页面上的元素进行动画处理时，浏览器经常要重新计算它们在文档中的位置和大小，从而触发布局。例如，如果改变了一个元素的宽度，它的任何一个子元素都可能受到影响，页面布局的很大一部分可能会改变。布局几乎总是适用于整个文档，所以布局树越大，它执行布局计算的时间就越长。

当动画元素时，必须尽量减少布局和重绘。并非所有的CSS动画技术都是一样的，现代浏览器可以通过位置、比例、旋转和不透明度来最好地创建性能优异的动画。

- 不要改变高度和宽度属性，而是使用transform：scale()。
- 要移动元素，避免改变top、right、bottom或left属性，而使用transform: translate()。
- 如果你想模糊背景，可以考虑使用模糊的图像并改变其不透明度。

##### 微调：contain属性

contain CSS 属性告诉浏览器，该元素及其子元素被认为是独立于文档树的（尽可能）。它将页面的子树与其他部分隔离开来。这样浏览器就可以优化页面独立部分的渲染（样式、布局和绘制操作）以提高性能。

contain 属性在包含许多独立小组件的页面上非常有用。可以使用它来防止每个小组件内的更改在小组件的边界框外产生副作用。一个大部分是静态的网站将不会从这个策略中得到什么好处。

#### 使用CSS优化字体加载

##### 避免在加载字体时出现不可见的文字

字体通常是需要一段时间来加载大文件。一些浏览器会隐藏文本，直到字体加载完毕（导致 "不可见文本的闪烁 "或FOIT）来处理这个问题。在优化速度时，你会希望避免 "不可见文本的闪烁"，并使用系统字体（预装在机器上的字体）立即向人们展示内容。一旦加载了字体文件，它就会取代被称为 "闪现的不规则文本 "或FOUT的系统字体。

实现这一目标的一种方法是使用font-display--一个用于指定字体显示策略的API。使用带有值交换的 font-display告诉浏览器应该立即使用系统字体显示使用此字体的文本。。

##### 使用可变字体以减少文件大小。

可变字体使字体的许多不同变化能够被整合到一个文件中，而不是为每一种宽度、重量或样式都有一个单独的字体文件。它们让您可以通过CSS和一个@font-face引用来访问一个给定字体文件中的所有变化。

当你需要多个字体时，可变字体可以显著减少文件大小。与其加载常规和粗体风格加上它们的斜体版本，你可以加载一个包含所有信息的单一文件。

Monotype做了一个实验，将12种输入字体组合起来，生成8种权重，横跨3种宽度，横跨斜体和罗马风格。将48种单独的字体存储在一个可变字体文件中，意味着文件大小减少了88%。

#### 不用担心CSS选择器的速度问题。

CSS选择符的结构方式会影响浏览器匹配它们的速度。浏览器从右到左读取选择符，所以当你使用后代选择器时。例如，nav a {}，它会首先匹配页面上的每一个 `<a>`元素，然后再将nav里面的元素归零。如果你使用一个更具体的选择器，例如，在nav元素内的每个 `<a>`上使用.nav-link，它就不会花时间去匹配页面上的每个 `<a>`。

如果你考虑浏览器是如何从右到左匹配选择符的，再举个例子，比如.container ul li a { }，你就会明白为什么后代选择器经常被贴上 "昂贵 "的标签。

看起来，这样的选择器会是一个速度问题。然而，选择器匹配性能是很快的。CSS声明对压缩算法非常友好，因此优化CSS选择器所需的努力通常会更好地用在应用程序的其他部分，投资回报率更高。

CSS对于加载页面和愉快的用户体验至关重要。虽然我们通常可能会优先考虑其他资源（如脚本或图像），因为它们更具影响力，但我们不应该忘记CSS。通过上述策略，您将能够确保快速交付和执行。

 