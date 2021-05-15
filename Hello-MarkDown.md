---
title: 认识与入门MarkDown
author: Te_Lee(Gaein nidb转载)
tags: 
- 软件教程
- 语言
categories: [软件教程]
date: 2019-04-28 00:23:38
---

[Markdown](https://zh.wikipedia.org/wiki/Markdown)是一种轻量级的 **「标记语言」**，它的优点很多，目前也被越来越多的写作爱好>者，撰稿者广泛使用。看到这里请不要被 **「标记」**、 **「语言」** 所迷惑，Markdown 的语法十分简单。常用的标记符号也不超过十个，这种相对于更为复杂的 HTML 标记语言来说，Markdown 可谓是十分
轻量的，学习成本也不需要太多，且一旦熟悉这种语法规则，会有一劳永逸的效果。

根据作者

> 转载请注明原作者

转载

作者：Te_Lee 

原文：https://www.jianshu.com/p/1e402922ee32

如有侵权请联系删除

**转载请注明原作者，如果你觉得这篇文章对你有帮助或启发，也可以来请我[喝咖啡](https://link.jianshu.com?t=http://ww2.sinaimg.cn/large/6aee7dbbjw1eiixgkex2ij21kw0t7wn5.jpg)**

## 导语

> [Markdown](https://zh.wikipedia.org/wiki/Markdown)是一种轻量级的 **「标记语言」**，它的优点很多，目前也被越来越多的写作爱好>者，撰稿者广泛使用。看到这里请不要被 **「标记」**、 **「语言」** 所迷惑，Markdown 的语法十分简单。常用的标记符号也不超过十个，这种相对于更为复杂的 HTML 标记语言来说，Markdown 可谓是十分
轻量的，学习成本也不需要太多，且一旦熟悉这种语法规则，会有一劳永逸的效果。


<img src="https://ww3.sinaimg.cn/large/6aee7dbbjw1eqft66xcg3j21kw12mdub.jpg" data-original-src="http://ww3.sinaimg.cn/large/6aee7dbbjw1eqft66xcg3j21kw12mdub.jpg" alt="[Ulysses for Mac](http://www.ulyssesapp.com)" style="cursor: zoom-in;"><div class="image-caption">[Ulysses for Mac](http://www.ulyssesapp.com)</div>
</div>
<h2>一，认识 Markdown</h2>
<p>在刚才的导语里提到，Markdown 是一种用来写作的轻量级<strong>「标记语言」</strong>，它用简洁的语法代替排版，而不像一般我们用的字处理软件 <em>Word</em> 或 <em>Pages</em> 有大量的排版、字体设置。它使我们专心于码字，用「标记」语法，来代替常见的排版格式。例如此文从内容到格式，甚至插图，键盘就可以通通搞定了。目前来看，支持 Markdown 语法的编辑器有很多，包括很多网站（例如<a href="https://www.jianshu.com" target="_blank">简书</a>）也支持了 Markdown 的文字录入。Markdown 从写作到完成，导出格式随心所欲，你可以导出 HTML 格式的文件用来网站发布，也可以十分方便的导出 PDF 格式，这种格式写出的简历更能得到 HR 的好感。甚至可以利用 <a href="https://link.jianshu.com?t=http://www.getcloudapp.com" target="_blank" rel="nofollow">CloudApp</a> 这种云服务工具直接上传至网页用来分享你的文章，全球最大的轻博客平台 <a href="https://link.jianshu.com?t=http://te1ee.tumblr.com" target="_blank" rel="nofollow">Tumblr</a>，也支持 Mou 这类 Markdown 工具的直接上传。</p>
<h3>Markdown 官方文档</h3>
<blockquote>
<p>这里可以看到官方的 Markdown 语法规则文档，当然，<strong>后文我也会用自己的方式阐述这些语法的具体用法</strong>。</p>
</blockquote>
<ul>
<li><a href="https://link.jianshu.com?t=http://daringfireball.net/projects/markdown/syntax" target="_blank" rel="nofollow"><em>创始人 John Gruber 的 Markdown 语法说明</em></a></li>
<li><a href="https://link.jianshu.com?t=http://wowubuntu.com/markdown/##list" target="_blank" rel="nofollow"><em>Markdown 中文版语法说明</em></a></li>
</ul>
<h3>使用 Markdown 的优点</h3>
<ul>
<li>专注你的文字内容而不是排版样式，安心写作。</li>
<li>轻松的导出 HTML、PDF 和本身的 .md 文件。</li>
<li>纯文本内容，兼容所有的文本编辑器与字处理软件。</li>
<li>随时修改你的文章版本，不必像字处理软件生成若干文件版本导致混乱。</li>
<li>可读、直观、学习成本低。</li>
</ul>
<h3>使用 Markdown 的误区</h3>
<blockquote>
<p>*We believe that writing is about content, about what you want to say – not about fancy formatting. *</p>
</blockquote>
<p>我们坚信写作写的是内容，所思所想，而不是花样格式。<br>
— <em>Ulysses for Mac</em></p>
<ul>
<li>Markdown 旨在简洁、高效，也由于 Markdown 的易读易写，人们用不同的编程语言实现了多个版本的解析器和生成器，这就导致了目前不同的 Markdown 工具集成了不同的功能（基础功能大致相同），例如流程图与时序图，复杂表格与复杂公式的呈现，虽然功能的丰富并没有什么本质的缺点，但终归有些背离初衷，何况在编写的过程中很费神，不如使用专业的工具撰写来的更有效率，所以如果你需实现复杂功能，专业的图形界面工具会更加方便。<strong>当然，如果你对折腾这些不同客户端对 Markdown 的定制所带来高阶功能感到愉悦的话，那也是无可厚非的。</strong>
</li>
</ul>
<div class="image-package">
<img src="https://ww2.sinaimg.cn/large/6aee7dbbgw1eq320claw3j21kw0kjdpc.jpg" data-original-src="http://ww2.sinaimg.cn/large/6aee7dbbgw1eq320claw3j21kw0kjdpc.jpg" alt="[flowchart.js on Github（使用 Markdown 绘制流程图）](https://github.com/adrai/flowchart.js)" style="cursor: zoom-in;"><div class="image-caption">[flowchart.js on Github（使用 Markdown 绘制流程图）](https://github.com/adrai/flowchart.js)</div>
</div>
<h3>我该用什么工具？</h3>
<div class="image-package">
<img src="https://ww2.sinaimg.cn/large/6aee7dbbgw1esvkj19bqmj20e80e874z.jpg" data-original-src="http://ww2.sinaimg.cn/large/6aee7dbbgw1esvkj19bqmj20e80e874z.jpg" alt="Mou for Mac" style="cursor: zoom-in;"><div class="image-caption">Mou for Mac</div>
</div>
<ul>
<li>在 Mac OS X 上，我强烈建议你用 <a href="https://link.jianshu.com?t=http://mouapp.com" target="_blank" rel="nofollow">Mou</a> 这款免费且十分好用的 Markdown 编辑器，它支持<strong>实时预览</strong>，既左边是你编辑 Markdown 语言，右边会实时的生成预览效果。不仅如此，Mou 还有一些有趣的偏好设置（<em>Preference</em>），例如主题（<em>Themes</em>）与样式（<em>CSS</em>），它们可以配置出定制化的文本编辑效果与导出效果，如果你对自带的主题与样式不满意还可以到 <a href="https://link.jianshu.com?t=https://github.com/search?utf8=%E2%9C%93&amp;q=Mou" target="_blank" rel="nofollow">GitHub</a> 上搜索其它爱好者为 Mou 编写的更多主题样式，导入的方式可以在偏好设置的 Themes 或 CSS 选项中 选择 reload。</li>
</ul>
<div class="image-package">
<img src="https://ww1.sinaimg.cn/large/6aee7dbbgw1effcq2gx92j210j0ustj7.jpg" data-original-src="http://ww1.sinaimg.cn/large/6aee7dbbgw1effcq2gx92j210j0ustj7.jpg" alt="Mou 的编写与预览窗口" style="cursor: zoom-in;"><div class="image-caption">Mou 的编写与预览窗口</div>
</div>
<p>如果你从事文字工作，我强烈建议你购买 <em><a href="https://link.jianshu.com?t=http://www.ulyssesapp.com" target="_blank" rel="nofollow">Ulysses for Mac</a></em>，这款软件入围了苹果 Mac App Store 的 <em>The Best of 2013</em>。它支持更多的写作格式、多文档的支持。Mou，iA writer 这些软件都是基于单文档的管理方式，而 Ulysses 支持 Folder、Filter 的管理，一个 Folder 里面可以创建多个 Sheet，Sheet 之间也可以进行 Combine 处理。</p>
<div class="image-package">
<img src="https://ww1.sinaimg.cn/large/6aee7dbbjw1eqgrj7suqoj217a0aiq4u.jpg" data-original-src="http://ww1.sinaimg.cn/large/6aee7dbbjw1eqgrj7suqoj217a0aiq4u.jpg" alt="Mac 上一些 Markdown 编辑器" style="cursor: zoom-in;"><div class="image-caption">Mac 上一些 Markdown 编辑器</div>
</div>
<ul>
<li><p>由于笔者很少接触 Windows，Windows 下的 Markdown 没有过多涉猎，经朋友介绍，有两款还算不错，一款叫做 <a href="https://link.jianshu.com?t=http://www.markdownpad.com" target="_blank" rel="nofollow">MarkdownPad</a> ，另一款叫做 <a href="https://link.jianshu.com?t=http://code52.org/DownmarkerWPF/" target="_blank" rel="nofollow">MarkPad</a>。</p></li>
<li><p>iOS 端很多 app 早已经支持了 Markdown 录入，例如 Drafts，Day One，iA writer 等，另外 Ulysses for iPad 现在已经上架，可以说是 iOS 平台最好的编辑器了。</p></li>
<li><p>在 Web端，我强烈推荐<a href="https://www.jianshu.com" target="_blank">简书</a>这款产品，上面有无数热爱文字的人在不停的创造，分享。在 Web 端使用 Markdown 没有比简书更舒服的地方了，同样支持左右两栏的实时预览，字体优雅，简洁。</p></li>
</ul>
<div class="image-package">
<img src="https://ww2.sinaimg.cn/large/6aee7dbbgw1effdkfijo1j21220nigth.jpg" data-original-src="http://ww2.sinaimg.cn/large/6aee7dbbgw1effdkfijo1j21220nigth.jpg" alt="简书的编辑预览模式" style="cursor: zoom-in;"><div class="image-caption">简书的编辑预览模式</div>
</div>
<h2>二，Markdown 语法的简要规则</h2>
<h3>标题</h3>
<div class="image-package">
<img src="https://ww1.sinaimg.cn/large/6aee7dbbgw1effeaclhiyj20eh09cwez.jpg" data-original-src="http://ww1.sinaimg.cn/large/6aee7dbbgw1effeaclhiyj20eh09cwez.jpg" alt="标题" style="cursor: zoom-in;"><div class="image-caption">标题</div>
</div>
<p>标题是每篇文章都需要也是最常用的格式，在 Markdown 中，如果一段文字被定义为标题，只要在这段文字前加 <code>##</code> 号即可。</p>
<p><code>## 一级标题</code></p>
<p><code>#### 二级标题</code></p>
<p><code>###### 三级标题</code></p>
<p>以此类推，总共六级标题，建议在井号后加一个空格，这是最标准的 Markdown 语法。</p>
<h3>列表</h3>
<p>熟悉 HTML 的同学肯定知道有序列表与无序列表的区别，在 Markdown 下，列表的显示只需要在文字前加上 <code>-</code> 或 <code>*</code> 即可变为无序列表，有序列表则直接在文字前加<code>1.</code> <code>2.</code> <code>3.</code> 符号要和文字之间加上一个字符的空格。</p>
<div class="image-package">
<img src="https://ww4.sinaimg.cn/large/6aee7dbbgw1effew5aftij20d80bz3yw.jpg" data-original-src="http://ww4.sinaimg.cn/large/6aee7dbbgw1effew5aftij20d80bz3yw.jpg" alt="无序列表与有序列表" style="cursor: zoom-in;"><div class="image-caption">无序列表与有序列表</div>
</div>
<h3>引用</h3>
<p>如果你需要引用一小段别处的句子，那么就要用引用的格式。</p>
<blockquote>
<p>例如这样</p>
</blockquote>
<p>只需要在文本前加入 <code>&gt;</code> 这种尖括号（大于号）即可</p>
<div class="image-package">
<img src="https://ww3.sinaimg.cn/large/6aee7dbbgw1effezhonxlj20e009c3yu.jpg" data-original-src="http://ww3.sinaimg.cn/large/6aee7dbbgw1effezhonxlj20e009c3yu.jpg" alt="引用" style="cursor: zoom-in;"><div class="image-caption">引用</div>
</div>
<h3>图片与链接</h3>
<p>插入链接与插入图片的语法很像，区别在一个 <code>!</code>号</p>
<p>图片为：<code>![]()</code></p>
<p>链接为：<code>[]()</code></p>
<p>插入图片的地址需要图床，这里推荐<a href="https://link.jianshu.com?t=http://weibotuchuang.sinaapp.com" target="_blank" rel="nofollow">围脖图床修复计划</a> 与 <a href="https://link.jianshu.com?t=http://www.getcloudapp.com" target="_blank" rel="nofollow">CloudApp</a> 的服务，生成URL地址即可。</p>
<div class="image-package">
<img src="https://ww2.sinaimg.cn/large/6aee7dbbgw1efffa67voyj20ix0ctq3n.jpg" data-original-src="http://ww2.sinaimg.cn/large/6aee7dbbgw1efffa67voyj20ix0ctq3n.jpg" alt="URL 与图片" style="cursor: zoom-in;"><div class="image-caption">URL 与图片</div>
</div>
<h3>粗体与斜体</h3>
<p>Markdown 的粗体和斜体也非常简单，用两个 <code>*</code> 包含一段文本就是粗体的语法，用一个 <code>*</code> 包含一段文本就是斜体的语法。</p>
<p>例如：<strong>这里是粗体</strong> <em>这里是斜体</em></p>
<h3>表格</h3>
<p>表格是我觉得 Markdown 比较累人的地方，例子如下：</p>
<pre class="hljs ruby"><code class="ruby"><span class="hljs-params">| Tables        |</span> Are           <span class="hljs-params">| Cool  |</span>
<span class="hljs-params">| ------------- |</span><span class="hljs-symbol">:-------------</span><span class="hljs-symbol">:|</span> -----<span class="hljs-symbol">:|</span>
<span class="hljs-params">| col 3 is      |</span> right-aligned <span class="hljs-params">| $1600 |</span>
<span class="hljs-params">| col 2 is      |</span> centered      <span class="hljs-params">|   $12 |</span>
<span class="hljs-params">| zebra stripes |</span> are neat      <span class="hljs-params">|    $1 |</span>
</code></pre>
<p>这种语法生成的表格如下：</p>
<table>
<thead>
<tr>
<th>Tables</th>
<th style="text-align:center">Are</th>
<th style="text-align:right">Cool</th>
</tr>
</thead>
<tbody>
<tr>
<td>col 3 is</td>
<td style="text-align:center">right-aligned</td>
<td style="text-align:right">$1600</td>
</tr>
<tr>
<td>col 2 is</td>
<td style="text-align:center">centered</td>
<td style="text-align:right">$12</td>
</tr>
<tr>
<td>zebra stripes</td>
<td style="text-align:center">are neat</td>
<td style="text-align:right">$1</td>
</tr>
</tbody>
</table>
<h3>代码框</h3>
<p>如果你是个程序猿，需要在文章里优雅的引用代码框，在 Markdown下实现也非常简单，只需要用两个 ` 把中间的代码包裹起来。图例：</p>
<div class="image-package">
<img src="https://ww3.sinaimg.cn/large/6aee7dbbgw1effg1lsa97j20lt0a8dgs.jpg" data-original-src="http://ww3.sinaimg.cn/large/6aee7dbbgw1effg1lsa97j20lt0a8dgs.jpg" style="cursor: zoom-in;"><div class="image-caption"></div>
</div>
<p>使用 <code>tab</code> 键即可缩进。</p>
<h3>分割线</h3>
<p>分割线的语法只需要三个 <code>*</code> 号，例如：</p>
<hr>
<blockquote>
<p>到这里，Markdown 的基本语法在日常的使用中基本就没什么大问题了，只要多加练习，配合好用的工具，写起东西来肯定会行云流水。更多的语法规则，其实 Mou 的 Help 文档栗子很好，当你第一次使用 Mou 时，就会显示该文档。可以用来对用的查找和学习。</p>
</blockquote>
<div class="image-package">
<img src="https://ww3.sinaimg.cn/large/6aee7dbbgw1effgmnpgqlj210j0us44j.jpg" data-original-src="http://ww3.sinaimg.cn/large/6aee7dbbgw1effgmnpgqlj210j0us44j.jpg" style="cursor: zoom-in;"><div class="image-caption"></div>
</div>
<h2>
<strong>三，相关推荐</strong>:</h2>
<h3><strong>工具</strong></h3>
<p><em>图床工具用来上传图片获取 URL 地址</em></p>
<ul>
<li><a href="https://link.jianshu.com?t=http://droplr.com" target="_blank" rel="nofollow">Droplr</a></li>
<li><a href="https://link.jianshu.com?t=http://www.getcloudapp.com" target="_blank" rel="nofollow">Cloudapp</a></li>
<li><a href="https://link.jianshu.com?t=https://itunes.apple.com/cn/app/yi-xiang/id672522335?mt=12&amp;uo=4" target="_blank" rel="nofollow">ezShare for Mac</a></li>
<li><a href="https://link.jianshu.com?t=http://weibotuchuang.sinaapp.com" target="_blank" rel="nofollow">围脖图床修复计划</a></li>
</ul>
<p><em>在线好用的Markdown工具，为印象笔记而生</em></p>
<ul>
<li><a href="https://link.jianshu.com?t=http://maxiang.info" target="_blank" rel="nofollow">马克飞象，专为印象笔记打造的Markdown编辑器，非常推荐</a></li>
</ul>
<h3>
<strong>相关文章阅读</strong>：</h3>
<ul>
<li><p><a href="https://www.jianshu.com/p/qqgjln" target="_blank">为什么作家应该用 Markdown 保存自己的文稿</a></p></li>
<li><p><a href="https://link.jianshu.com?t=http://www.yangzhiping.com/tech/r-markdown-knitr.html" target="_blank" rel="nofollow">Markdown写作浅谈</a></p></li>
<li><p><a href="https://link.jianshu.com?t=http://www.appinn.com/markdown-tools/" target="_blank" rel="nofollow">Markdown 工具补完</a></p></li>
<li><p><a href="https://www.jianshu.com/p/63HYZ6" target="_blank">Drafts + Scriptogr.am + Dropbox 打造移动端 Markdown 风格博客</a></p></li>
<li><p><a href="https://link.jianshu.com?t=http://www.ituring.com.cn/article/23" target="_blank" rel="nofollow">图灵社区，怎样使用Markdown</a></p></li>
<li><p><a href="https://link.jianshu.com?t=http://news.cnblogs.com/n/139649/" target="_blank" rel="nofollow">为什么我们要学习Markdown的三个理由</a></p></li>
<li><p><a href="https://link.jianshu.com?t=http://ibuick.me/?p=4093" target="_blank" rel="nofollow">Markdown 语法写作入门指南 by ibuick</a></p></li>
</ul>
<hr>
<p><strong>本文首发在<a href="https://link.jianshu.com?t=http://sspai.com/25137" target="_blank" rel="nofollow">少数派</a>转载请注明原作者，如果你觉得这篇文章对你有帮助或启发，也可以来请我<a href="https://link.jianshu.com?t=http://ww2.sinaimg.cn/large/6aee7dbbjw1eiixgkex2ij21kw0t7wn5.jpg" target="_blank" rel="nofollow">喝咖啡</a>。<br>
利益相关：本篇文章所有涉及到的软件均为笔者日常所用工具，无任何广告费用。</strong></p>

          </div>
        </div>
