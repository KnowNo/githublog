## SCSS 一站式入门

长久以来，前端开发的语言一直以 HTML + CSS + JavaScript 为主，其中 HTML 和 CSS 的语法很是简单，分分钟上手，但同时其功能也少的可怜。以 CSS 为例，仅仅支持继承、导入和媒体查询等比较「智能」的写法，写 CSS 基本上是规则的堆砌。

在 CSS 3 时代，以往的 CSS 写法显然有些捉襟见肘，有谁会愿意用五行代码去写一个属性：

	.box{
		-webkit-border-radius: 5px;
		-moz-border-radius: 5px;
		-ms-border-radius: 5px;
		-o-border-radius: 5px;
		border-radius: 5px;
	}

有人说「懒惰是推动科技进步的重要因素」，而「程序员尤其懒」。

SCSS 就是为了在写 CSS 时更加「偷懒」而诞生的。不光是写得快，而且还写得「有态度」。SCSS 在 CSS 中注入了编程思想，使 CSS 如虎添翼。

本文主要对 SCSS 的如下方面进行介绍：

1. 语法
2. 编译
3. IDE

### 语法

SCSS 的完整语法很多，本人摘出其中最实用几个略做讲解，相信你半个小时节能学会 SCSS 的大部分了 :)

缩减后的列表如下：

> + CSS 扩展
> 	- 嵌套选择器
> 	- 父选择器：&
> + SCSS 脚本
> 	- 变量：$
> 	- 插值：#{}
> + @ 指令
> 	- @import 
> 	- @extend
> + Mixin（混入指令）
> 	- 定义 mixin：@mixin
> 	- 使用 mixin：@include

这些功能能满足几乎所有需求。

#### 1. 嵌套选择器

以前的

	#main { color: #000; }
	#main p { color: #00ff00; width: 97%; }
	#main p .redbox { background-color: #ff0000; color: #000000; }

用 SCSS 为

	#main { color: #000;
		p { color: #00ff00; width: 97%;
			.redbox { background-color: #ff0000; color: #000000; }
		}
	}

#### 2. 父选择器：&

	.link { color: #DDD; }
	.link:hover { color: red; }

用 SCSS 为

	.link { color: #DDD; 
		&:hover { color: red; }
	}

#### 3. 变量：$

这次不对照 CSS 相信你也能看懂：

	$box-width: 100px;
	
	.box { width: $box-width; }
	.little-box { width: $box-width / 2; }

没错，上面用到了除法，这也是 CSS 所不具备的。

#### 4. @import

SCSS 中的 @import 和 CSS 的差不多，会引入一个文件。

	@import "foo"; 
	@import "foo.scss"; /* 没有后缀时会默认为 scss 文件，所以这两行等价。 */
	@import "foo.css";
	@import "http://foo.com/bar.css";
	@import url(foo);

不过 SCSS 更高级的地方是，你可以在任何地方引入一个文件，而不仅仅是文件开头。

	#main {
		@import "example.scss";
	}

曾经我们经常被教导：不要使用 @import。因为 CSS 里的 @import 会发起网络请求，而且效果又不能令人满意。但是在 SCSS 里就不存在这个问题了，因为 SCSS 可以在本地把导入的 SCSS 文件合并成单独一个 CSS 文件。

#### 5. @extend

SCSS 中用 @extend 指令来实现选择器间的继承：

	.error { border: 1px #f00; background-color: #fdd; }
	.seriousError { 
		@extend .error; 
		border-width: 3px; 
	}

编译为的 CSS 为

	.error, .seriousError { border: 1px #f00; background-color: #fdd; }
	.seriousError { border-width: 3px; }

可以把 @extend 理解为：凡是出现 .error 选择器的地方都会出现 .seriousError 选择器，如此一来，.error 拥有的所有属性，.seriousError 也同样拥有。

#### 6. @mixin 和 插值：#{}

回到之前说的一段 CSS 代码

	.box{
		-webkit-border-radius: 5px;
		-moz-border-radius: 5px;
		-ms-border-radius: 5px;
		-o-border-radius: 5px;
		border-radius: 5px;
	}

我们可以使用 @mixin 把它变得「可复用」，而不用每次都敲那么多前缀

	$prefixes : -webkit-, -moz-, -ms-, -o-,"";
	
	@mixin border-radius ($radius) {
    	@each $pre in $prefixes {
        	#{$pre}border-radius:$radius;
    	}
	}

下次要写 border-radius 时只需一句即可

	.box {
		@include border-radius (5px);
		@include border-radius (10px);
	}

如果再深入挖掘，我希望在写 box-shadow、transition、transform 等 CSS 3 属性时能不能也复用一个方法呢？请看下面

	$prefixes : -webkit-, -moz-, -ms-, -o-,"";

	@mixin prefix($name, $argument) {
		@each $pre in $prefixes {
        	#{$pre}#{$name}:$argument;
    	}
	}

好了，现在所有前缀都不需要自己加了

	div {
		@include prefix(border-radius, 5px );
		@include prefix(transform, rotate(7deg) );
		@include prefix(transition, width 2s );
	}

现在你是不是发现用编程的思想写 CSS 的好处多多啦？那就赶紧试试吧。

### 编译

SCSS 其实是类 CSS 语法的 SASS，而 SASS 又是什么呢？SASS —— Syntactically Awesome Stylesheets，直译为「语法犀利的样式表」。它原本是基于 Ruby 的。

所以要想把写好的 SCSS 变成 CSS，可以借助与 Ruby 的 SASS 编译器。下面我们来看看如何安装。

1.首先当然是安装 Ruby。你可以到 [Ruby 的中文主页](http://www.ruby-lang.org/zh_cn/downloads/) 下载 Ruby 的 Window 安装器。一直点下一步就可以安装了。

2.然后就可以按照 [SASS 官网的教程](http://sass-lang.com/tutorial.html) 来安装 SASS 了。打开命令行（点击「开始」-> 运行 -> 输入 cmd -> 回车），输入 ``gem install sass``。基本上，如果你在公司运行这个命令，一定会看到错误提示。不要紧，这是因为我们没有设置代理，在命令行输入``set HTTP_PROXY=http://proxy.tencent.com:8080`` 即可。然后重新 ``gem install sass`` 试试。

3.安装成功后你就可以在命令行输入 ``sass -v`` 看看你安装成功没有了。如果你看到类似 ``Sass 3.2.7 (Media Mark)`` 的提示就说明成功了！如果不成功的话也许你可以把 Ruby/bin/ 目录加入环境变量 PATH。

4.这一步我们把一个 SCSS 文件编译成一个 CSS 文件。首先在 C 盘新建一个 test.scss 文件，其内容为 

	.main { color: #000;
		p { color: #00ff00; width: 97%;
			.redbox { background-color: #ff0000; color: #000000; }
		}
	}

然后再命令行中定位到此文件所在目录，输入
 
```sass test.scss```

你会看到它把得到的 CSS 内容直接输入在屏幕上了。如果想把 CSS 内容保存到文件，可以输入 

```sass test.scss:test.css```

当前目录就会新建一个 test.css 文件了。

5.难道没改动一次 SASS 文件都要去运行一次命令行把它变成 CSS 吗？这里有一个监听文件变化的方法

```sass --watch test.scss:test.css```

这样一来，你每次**保存** test.scss 文件时，test.css 文件都会更新。你甚至可以监听一整个目录

```sass --watch ./scss/:./css/```

那么 scss 目录中的任意一个文件改变时，css 目录中也相应的更新，是不是很方便~

细心的童鞋肯定会发现每次编译时都会多出一个 .sass-cache 目录，很是烦人，你只要在编译时加上 ``--no-cache`` 参数即可

```sass --watch --no-cache ./scss/:./css/```

6.一点点问题。在使用 SCSS 的过程中我发现它居然不支持中文，想来肯定是 Ruby 的设置有问题，最终怎网上找到了解决办法。首先在 Windows 的环境变量中添加 ``RUBYOPT`` 变量，值设为 ``-KU`` 。然后在你的 SCSS 文件第一行加上 ``@charset "UTF-8";``，再编译就能完美支持中文了。（ Ruby 达人能告诉我这是为什么吗？）


### IDE

命令行这玩意并不讨所有人喜欢，不过幸好各主流 IDE（如 [WebStorm，我强烈推荐这款 IDE](http://www.oschina.net/shop/jetbrains)，很适合前端开发 ）和编辑器（如 Sublime Text）对 SASS 都提供了语法高亮和编译功能。

在 Sublime 中安装 SASS Build 插件后，点击 Tool，在 Build System 中选择 SASS，然后点击 Build，即可把当前的 scss 文件编译为 css 文件（css 文件处于同目录）。你可以对此插件进行一些修改使它支持 --watch 和 --no-cache 选项。（找到 SASS.sublime-build 文件，修改其 cmd 对应的值即可。）

WebStorm 默认就支持 SASS 和 SCSS 语法高亮。编译可以借助它的「外部工具」（External Tools）。点击 file -> settings -> 找到 External Tools -> 在右边点加号新增一个 Tool -> 看到 Edit Tool 对话框 -> 如下图所示填写对话框。

![截图](http://d.pr/i/55k6+)

填好之后保存。之后打开 scss 文件，在主界面点击 Tool -> build -> SASS，即可编译整个 scss 目录至 css 目录。
