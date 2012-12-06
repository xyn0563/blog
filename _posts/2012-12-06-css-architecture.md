---
layout: default
title: learning principles for improving you css 
---
# CSS架构 #
## 好的CSS架构想要达到的目的 ##

好的CSS架构应该和其他的软件架构达到同样的目的。那就是预见性(predictable), 重用性(reusable), (维护性)maintainable, 以及可扩展性(scalable)。

### 可预见性 ###

可预见性是指，你的CSS按照你所预期的那样工作。当你添加，或是更新了你的CSS规则时，它不会影响到其他你不想影响到的部分。对于小型站点来说这没什么，但是对于拥有成千上万页的大型网站来说，这是必须要做到的。

### 重用性 ###

CSS规则应该是被充分抽象、去耦(decoupled)过的，这样可以使你很快地用现有的代码构造一个新的组件，而不用重复工作。

### 可维护性 ###

当网站需要增加新功能和新组件，或者需要对它们进行升级、重新排列时，不需要重构你的css代码。在页上增加一个组件X时，不能影响原有的组件Y。

### 可扩展性 ###
当你的站点越来越大，越来越复杂时，通常需要更多的开发者来共同维护。可扩展性意味着，单个工程师，或是一个庞大的团队都可以容易地维护css代码。同时意味你的css代码通俗易懂，不需要花费太大的学习成本。 今天你写下了一段css代码，并不是说你得管上一辈子。

## 常见的不好的做法 ##

在讲述如何构建一个好的CSS架构之前，先来看一些反面的例子。人们常常在多次的重复一个错误之后，才会去开始另一种做法。

### 基于组件的复元素来修改组件的样式 ###

在一个网站中，通常会有一些元素，它们在所有场合下的样式是完全一样的。当面对这种情形时，新手们(抑或一些有经验的老鸟)通常会这么做：找到组件的一个父级元素(可以同其他元素区别开)，利用它来重写样式。

  .widget {
    background: yellow;
    border: 1px solid black;
    color: black;
    width: 50%;
  }

  #sidebar .widget {
    width: 200px;
  }

  body.homepage .widget {
    background: white;
  }

看上去没啥问题。但是让我们以之前提到的好架构的标准来衡量一下它。

首先，这个组件不具有可预见性。同样一个组件(.widget)，我把它放在siderbar或是homepage时，表现却是不一样的，尽管它们的html结构都一样的。

同时这个组件也没有可重用性和可扩展性。万一在其他页面上又要用到这个组件，而需要它和homepage上的样式一样，这时该怎么办？那就必须得添加新的规则。

同时，可维护性也差。当组件改设计时，css里面相应地要进行多处修改。

想像一下，上面的代码是通过其他语言来实现的，那么基本上你先声明了一个类，然后在其他的场合下又对它进行了重新定义。这严重违反了软件开发的一个基本原则：

  Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

后面会提到如何不依赖父元素来修改组件样式。

### 过于复杂的选择器 ###

有些文章会告诉你，css选择器是如此的强大，你可以不用任何的class和id规则，仅靠选择器就能完成整个网站的样式。

技术角度来说的确可行。但是随着(作者)css写得越来越多，就越会避免使用复杂的css选择器。

选择器复杂，意味着HTML结构同样复杂。依赖HTML标签和选择符可以保持你的HTML整洁，但会使你的css看上去乱七八糟。

  #main-nav ul li ul li div { }
  #content article h1:first-child { }
  #sidebar > div > h3 + p { }

以上代码从逻辑上都是可行的。如果说HTML结构万年不变，那么这种写法没问题。但这种事情太不现实了。复杂的选择器令人印象深刻，同时避免了HTML代码充斥各种样式挂钩，但是对于构建好的css架构来说，没有什么好处。

以上代码几乎没办法重用。拿第一个例子来说，如果另外一个页面里也有一个下拉菜单，样式一样，但并不包含在#main-nav里，这时就没办法了，只能重新写一个规则。

这些代码也没有可预见性。如果想把div换成html5的section标签，那整个样式完全用不了了。

最后，由于这些规则可能应用于特定的html结构，也给维护带来的困难，同时不具有任何可扩展性。

### 过于普通(易重名)的class名称 ###

  <div class="widget">
    <h3 class="title">...</h3>
    <div class="contents">
      Lorem ipsum dolor sit amet, consectetur adipiscing elit.
      In condimentum justo et est dapibus sit amet euismod ligula ornare.
      Vivamus elementum accumsan dignissim.
      <button class="action">Click Me!</button>
    </div>
  </div>

  .widget {}
  .widget .title {}
  .widget .contents {}
  .widget .action {}

像`.title`, `.contents`, `.action`这种子元素的类名，它不会影响到别的相同名的样式。但是其他同名的样式却有可能影响到这个组件。

在一个大型项目里，`.title` 样式很可能在其他场合被定义了，这样就会影响到组件本身的样式。

所以说，过于普通的class名称，可预见性很差。

### 一条规则里做过多的事情 ###

下面的样式定义了一个离左上角20px的元素。

  .widget {
    position: absolute;
    top: 20px;
    left: 20px;
    background-color: red;
    font-size: 1.5em;
    text-transform: uppercase;
  }

接下来，如果你想在其他的地方也用到这个组件，那么有些css就不会生效了。问题在于，这个样式里定义了太多的东西，除了外观之外，还有布局、定位。在其他的场合，外观的样式可以保留，不过布局和定位则不然。而由于这些样式都被定义到了一起，导致在其他场合需要重写整个样式。

这么写，一开始看上去没啥问题，但到后来会导致一遍又一遍地copy代码。比如说团队里来了个新同学，他按以上的方式实现了一个叫`.infobox`的东东。在另外的一个页面里，由于定位的原因导致`.infobox`样式不对。新手的处理方式往往式不是提取公用部分，而是将在当前情况下可用的样式copy到另一个新的样式中去以解决问题，导致了不必要的重复。

## 原因所在 ##

以上所有的不好的实践有一个共同的特点：让CSS承担了过多的样式的任务(they place far too much of the styling burden on the CSS)。

这句话很奇怪。毕竟，css就是干这个的，难道它不应该承担吗？这不正是我们想要的吗？

事情通常没这么简单。将内容和样式分离的做法是好的，但并不是说将css和html分开了就做到了这一点。换句话说，如果你的css和html结构需要密切配合的话，简单地把所有的样式代码抽取出来并不能达到目的。

更进一步说，html代码很多时候并不仅仅是内容，它也是结构。有些时候一个结构里包含了其他的容器，而这些窗口的作用仅仅是使css选择器可以准确地命中这些特定的元素。Even without presentational classes, this is still clearly presentation mixed into the HTML. But is it necessarily mixing presentation with content?

我相信，现在通过html和css，是可以，通常也是明智地，将html和css结合在一起构成一个表现层。同时内容层也可以通过模板或代码片断抽取出来。

## 解决方案 ##

如果说，在构建一个web应用时，将html和css结合在一起来构成它的表现层，就要遵循一个良好css架构的各种原则。

我(作者)发现最好的达到这一目的方法是，css代码里包含尽量少的html结构。css应该用来定义一组可见元素的显示样式，不管它们出现在哪里，都是相同的样式。如果一个组件在特定的情景下要显示成另外的样子，那么就把它定义成其他的组件，而这是html所负责的事情。

举个例子，用`.button`样式来定义一个按钮组件。如果想让一个html元素看上去像按钮，那么就使用这个样式。如果在某种场景下，按钮的表现样式变了(比如变大，占用整个宽度)，那么就需要另起一个新的样式来实现它，同时在html时包含这个新的样式来达到新的效果。

css定义了组件的样式，html将这些样式应用到页面的各个元素上去。css对于html结构知道的越少越好。

在html中声明它所应用的样式(class)，可以让其他开发者很容易就知道当前这个元素将会显示成什么样。否则的话，无法知道一个元素是故意被设计成这样的呢，还是不小心变成这样的，而这会让大伙觉得迷惑。

对于这种在html里放置大量css class的做法，也存在着异议。那就是这么做会需要花费更多额外的精力。一条css规则可能匹配很多元素，那么一次又一次地在html标签中写上一堆css类名是否值得呢？

这种担心显示是有道理的，它可能会产生误导。要么你将样式写在父级元素里，要么你在html标签里无数次地重复css类名。不过还有其他的解决方案。比如Rails或其他框架，它们对于View层的抽象，使得你不用每次都重复地去写相同的类名\[我理解的是在说模板\]。


## 最佳实践 ##

### 规避冲突 (be Intentional) ###

要是想让你的css规则避免误伤到其他元素，最好的方式就是别给它机会。像`#main-nav ul li ul li div`这种就很容易匹配到你不想匹配的元素。而像`.subnav`就不会。直接给你的目标元素赋上所需样式，在css的可预见性方面来说，是最佳实践。

  /* Grenade */
  #main-nav ul li ul { }

  /* Sniper Rifle */
  .subnav { }

上面的例子，第一行就像个手榴弹，它可以工作，但杀伤半径太大，有可能误伤到别人。而第二行就像一个狙击步枪，准确秒杀。

### 分而治之 (SEPARATE YOUR CONCERNS) ###

前面说了，结构良好的组件可以减少html和css的耦合。另外一点，你的css本身要模块化。组件本身知道如何渲染自己，但没有责任关心布局和定位，也没必要去关心周围环境的变化。

简单来说，组件要做的事情就是定义它本身的样式，而不是它们的布局、定位。所以当你在一个css规则里，有`background`, `color`, `font`这些标签的同时，又有`position`, `width`, `height`, `margin`时，就要小心了。

布局和定位应该由其他独立的布局样式或独立的容器来负责。

### 命名空间 ###

之前讲过了使用父级元素并不能有效地避免样式的交叉污染。一个好的方法就是使用命名空间。对于一个组件来说，所有它下面的子元素的样式名称都要以组件的样式名称做为命名空间。

  /* High risk of style cross-contamination */
  .widget { }
  .widget .title { }

  /* Low risk of style cross-contamination */
  .widget { }
  .widget-title { }

命名空间可以有效地避免样式冲突。

### 使用修饰符(modifier)来扩展组件  ###

对于已经存在的组件，要是在另外的场合中的表现略有差异，那么使用修饰符来对先前的组件进行扩展。

  /* Bad */
  .widget { }
  #sidebar .widget { }

  /* Good */
  .widget { }
  .widget-sidebar { }

### 使css结构更有逻辑性 ###

[Jonathan Snook](http://snook.ca/)在[SMACSS](http://smacss.com/)这本书里讨论了四种类型：基础类型(base), 布局(layout), 模块(modules)以及状态(state)。

*基础类型*包括css reset和元素的默认样式。*布局*指整站的页面结构，以及类似栅格系统的东西。*模块*就是可重用的部分。*状态*是可以通过Javascript来切换的状态，比如`.on`, `.off`。

在SMACSS系统中，模块(组件)是构成css的主要部分。所以经常需要将模块进行更深层次的抽象。




