+++
Description = "css下拉菜单 来自微软"
Tags = ["css","下拉菜单"]
date = "2011-05-15T10:16:01+08:00"
ds_title = "[转载]来自微软的纯CSS下拉菜单"
title = "[转载]来自微软的纯CSS下拉菜单"

+++
<div id="cnblogs_post_body">
<div>结合JavaScript的下拉菜单，纯CSS的下拉菜单我也写过很多了。不过在微软 Microsoft Expression Web 的相关站点上看到这个纯CSS下拉菜单的时候，我还是觉得很赞赏。这应该是最精简、最干净的纯CSS下拉菜单了。</div>
<div>
<div>先看一下效果（这是我重新实现的）：</div>
<div><a href="http://wukangrui.net/attachments/2009/04/image16.png" target="_blank"><img title="image" src="http://file.wukangrui.com/attachments/2009/04/image-thumb16.png" alt="image" width="516" height="145" border="0" /></a></div>
<div>下面是实现方法：</div>
<div>首先是菜单的XHTML代码：</div>
```html
<ul>
	<li><a href="#">菜单一</a></li>
	<li><a href="#">菜单二</a>
<ul>
	<li><a href="#">子菜单一</a></li>
	<li><a href="#">子菜单二</a></li>
	<li><a href="#">子菜单三</a></li>
</ul>
</li>
	<li><a href="#">菜单三</a></li>
	<li><a href="#">菜单四</a>
<ul>
	<li><a href="#">子菜单一</a></li>
	<li><a href="#">子菜单二</a></li>
	<li><a href="#">子菜单三</a></li>
</ul>
</li>
	<li><a href="#">菜单五</a></li>
</ul>
```

<div>不设置任何CSS类，实在是干净到极点了（当然，考虑到实际应用的复杂性，我估计你不可能真的什么都不加。要么把这段代码放到一个特定的容器里，要么给第一层的ul加一个id或者class。</div>
<div>假设这是在一个新的HTML文档里，那么我们现在没有任何的CSS定义，这时候的网页显示效果是这样的：</div>
<div><a href="http://wukangrui.net/attachments/2009/04/image17.png" target="_blank"><img title="Two Tier Unordered List" src="http://file.wukangrui.com/attachments/2009/04/image-thumb17.png" alt="Two Tier Unordered List" width="171" height="219" border="0" /></a></div>
<div>在我们的下拉菜单中，不需要内补丁、外边距这些东西，即使需要，我们也要自己重新设置，所以我们首先添加第一条规则：</div>
```css
ul {
    margin: 0px;
    padding: 0px;
}
```
<div>为了跨浏览器兼容，必须同时把外边距和内补丁都设置为0，因为有的浏览器默认使用外边距，有的则默认使用内补丁。这样设置以后，页面上可以看到两层列表项前面的缩进都没了，实心和空心的列表符号也不见了。然后设置第二条规则：</div>
```css
ul li {
    float: left;
    display: inline;
    font: 0.9em Arial, Helvetica, sans-serif;
    height: 30px;
    width: 100px;
    list-style: none;
}
```
<div>这是让原本各占一行的li元素变成嵌入式（inline）显示，另一种说法是把list-item元素变成行内元素。总而言之就是让li不要各占一行，并列起来，这样才能成为菜单。设置后，效果如下：</div>
<div><a href="http://wukangrui.net/attachments/2009/04/image18.png" target="_blank"><img title="image" src="http://file.wukangrui.com/attachments/2009/04/image-thumb18.png" alt="image" width="464" height="122" border="0" /></a></div>
<div>这样就得到了下拉菜单的雏形，当然了，外观很丑陋，下拉功能也还没实现。这里要说明的是，最好给菜单项设置高度和宽度，否则有可能出现不可预料的结果（取决于页面或容器的宽度）。为了让菜单项看起来像菜单，我们继续添加规则：</div>
```css
ul li a {
    color: #FFF;
    text-decoration: none;
    line-height: 29px;
    width: 91px;
    margin: 0px;
    padding: 0px 0px 0px 8px;
    display: block;
    border-right: solid 1px #ccc;
    border-bottom:solid 1px #ccc;
    background: #808080;
}
```
<div>这一条规则比较长，从作用上说呢，就是加上背景和菜单间的隔离线，把默认有下划线蓝色的文字变成白色无下划线。增加了规则后的效果：</div>
<div><a href="http://wukangrui.net/attachments/2009/04/image19.png" target="_blank"><img title="image" src="http://file.wukangrui.com/attachments/2009/04/image-thumb19.png" alt="image" width="519" height="135" border="0" /></a></div>
<div>从外观上，这就已经是我们最终的下拉菜单样式了。不过，我们还要在细节上修饰一下，比如让子菜单和一级菜单的样式有所区别（当然了，由于字体设置的0.9em，所以在文字大小上已经有区别了，但是还不够，而且对于中文来说，很多时候我们未必能通过文字大小来区别，因为适合中文的常规文字大小就12px和14px而已），所以我们修改一下子菜单的背景色，并且让子菜单比一级菜单的高度要小一些。规则：</div>
```css
ul li ul li { height:25px; }
ul li ul li a {
    background: #666;
    line-height:24px;
}
```
<div></div>
<div></div>
<div>这里包含两条规则，第一条是将子菜单的列表项目高度由之前统一设置的30px减小为25，第二是将子菜单项的背景改为#666，并且文字行高对应地减小到24设置完成后（列表项高度-1，减去的1正好是上边框的1像素），效果如下：</div>
<div><a href="http://wukangrui.net/attachments/2009/04/image20.png" target="_blank"><img title="image" src="http://file.wukangrui.com/attachments/2009/04/image-thumb20.png" alt="image" width="517" height="124" border="0" /></a></div>
<div>通常我们鼠标滑过某个菜单项的时候，要让它跟其它项目有所区别（表示当前菜单处于激活状态，术语叫“高亮”<img src="http://www.zu14.cn/coolemotion/emotions/zz_1.gif" alt="" border="0" /> ，所以我们对一级菜单的鼠标滑过样式做一下定义：</div>
<pre lang='html'>ul li a:hover { background: #666; }</pre>
<div>注意，这里为鼠标滑过时设置的背景色和子菜单的背景色一样，原因？看效果就知道了：</div>
<div><a href="http://wukangrui.net/attachments/2009/04/image21.png" target="_blank"><img title="image" src="http://file.wukangrui.com/attachments/2009/04/image-thumb21.png" alt="image" width="517" height="127" border="0" /></a></div>
<div>这里的第二个菜单项就是鼠标滑过时候的样式，这样就跟弹出的（现在还不会弹出）子菜单背景色一致了。现在整个菜单的样式都设置完了，但是，这只是静态的菜单啊，我们要的是会动的。所以工作还没完成。接下来是什么呢？当然是默认情况下把二级菜单隐藏起来。我们要写JS来隐藏他们吗？No！样式如下：</div>
```css
ul li ul { visibility: hidden; }
```
<div></div>
<div>这样，子菜单并不是像“display:none”一样不显示，它还是在那个位置，文档结构什么的都没有发生任何改变，只是看不到它而已，而且下拉菜单中的链接当然也没不可以点击。</div>
<div><a href="http://wukangrui.net/attachments/2009/04/image22.png" target="_blank"><img title="image" src="http://file.wukangrui.com/attachments/2009/04/image-thumb22.png" alt="image" width="528" height="53" border="0" /></a></div>
<div>最后一条规则，让鼠标滑过有下拉项的时候，显示下拉菜单。当然我们实际设置的是：如果某一个下拉菜单的父级元素（一级菜单项）被鼠标滑过，那么就让该下拉菜单可以被看见：</div>
```css
ul li:hover ul { visibility: visible; }
```
<div></div>
<div>这样就完成了整个设置下拉菜单制作，当然你还可以进一步修饰这个菜单，比如我们应该让子菜单中的项目在鼠标滑过的时候也变色：</div>
<pre lang='html'>ul li ul li a:hover { background: #333; }</pre>
<div></div>
<div>最终效果</div>
<div><a href="http://wukangrui.net/attachments/2009/04/image16.png" target="_blank"><img title="image" src="http://file.wukangrui.com/attachments/2009/04/image-thumb16.png" alt="image" width="516" height="145" border="0" /></a></div>
<div>要强调的一点：这个下拉菜单在各主流浏览器（IE6以下的版本除外）中的外观及行为都是完全一致的。兼容性非常好。为什么不支持IE6呢？因为IE8都出来了，我们是不是应该彻底抛弃那个给网页设计师带来无限痛苦的万恶的IE6了？</div>
<div>如果IE6的兼容性对你的站点来说非常重要，那么你可以参考这盘文章：<a href="http://wukangrui.com/2009/06/22/whatever-hover-pseudo-class-without-javascript.html" target="_blank">http://wukangrui.com/2009/06/22/whatever-hover-pseudo-class-without-javascript.html</a>。</div>
<div>本文参考：<a title="http://www.microsoft.com/china/expression/newsletter/2008-11/article05.aspx" href="http://www.microsoft.com/china/expression/newsletter/2008-11/article05.aspx" target="_blank">http://www.microsoft.com/china/expression/newsletter/2008-11/article05.aspx</a></div>
</div>
<div id="MySignature">本文首发自 <a href="http://wukangrui.com/" target="_blank">刀刀博客</a>，博客园同步更新。如需转载，请注明作者及出处。</div>
</div>
