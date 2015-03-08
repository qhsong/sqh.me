+++
Description = "字符编码进化"
Tags = ["字符编码"]
date = "2011-08-14T17:22:46+08:00"
ds_title = "字符编码详解 part2"
title = "字符编码详解 part2"

+++
<div>
编码系列1：<a href="http://sqh.me/blog/?p=263">编码那点事----历史进化</a>
编码系列2：<a href="http://sqh.me/blog/?p=273">字符编码详解 part1</a>
编码系列3：<a href="http://sqh.me/blog/?p=275">字符编码详解 part2</a>
本文选自<a href="http://polaris.blog.51cto.com/1146394/377468">http://polaris.blog.51cto.com/1146394/377468</a>
<strong>
6.1 UTF的字节序和BOM</strong></div>
<div></div>
<div><strong>6.1.1 字节序</strong></div>
<div></div>
<div>UTF-8以字节为编码单元，没有字节序的问题。UTF-16以两个字节为编码单元，在解释一个UTF-16文本前，首先要弄清楚每个编码单元的字节序。例如收到一个“奎”的Unicode编码是594E，“乙”的Unicode编码是4E59。如果我们收到UTF-16字节流“594E”，那么这是“奎”还是“乙”？</div>
<div></div>
<div>Unicode规范中推荐的标记字节顺序的方法是BOM。BOM不是“Bill Of Material”的BOM表，而是Byte Order Mark。BOM是一个有点小聪明的想法：</div>
<div></div>
<div>在UCS编码中有一个叫做"ZERO WIDTH NO-BREAK SPACE"的字符，它的编码是FEFF。而FFFE在UCS中是不存在的字符，所以不应该出现在实际传输中。UCS规范建议我们在传输字节流前，先传输字符"ZERO WIDTH NO-BREAK SPACE"。</div>
<div></div>
<div>这样如果接收者收到FEFF，就表明这个字节流是Big-Endian的；如果收到FFFE，就表明这个字节流是Little-Endian的。因此字符"ZERO WIDTH NO-BREAK SPACE"又被称作BOM。</div>
<div></div>
<div>UTF-8不需要BOM来表明字节顺序，但可以用BOM来表明编码方式。字符"ZERO WIDTH NO-BREAK SPACE"的UTF-8编码是EF BB BF（读者可以用我们前面介绍的编码方法验证一下）。所以如果接收者收到以EF BB BF开头的字节流，就知道这是UTF-8编码了。</div>
<div></div>
<div><strong>6.1.2 BOM</strong></div>
<div></div>
<div>（1）BOM的来历</div>
<div></div>
<div>为了识别 Unicode 文件，Microsoft 建议所有的 Unicode 文件应该以 ZERO WIDTH NOBREAK SPACE（U+FEFF）字符开头。这作为一个“特征符”或“字节顺序标记（byte-order mark，BOM）”来识别文件中使用的编码和字节顺序。</div>
<div></div>
<div>（2）不同的系统对BOM的支持</div>
<div></div>
<div>因为一些系统或程序不支持BOM，因此带有BOM的Unicode文件有时会带来一些问题。</div>
<div></div>
<div>①JDK1.5以及之前的Reader都不能处理带有BOM的UTF-8编码的文件，解析这种格式的xml文件时，会抛出异常：Content is not allowed in prolog。“对于解决方法，之后我会写篇文章专门讨论该问题。”</div>
<div></div>
<div>②Linux/UNIX 并没有使用 BOM，因为它会破坏现有的 ASCII 文件的语法约定。</div>
<div></div>
<div>③不同的编辑工具对BOM的处理也各不相同。使用Windows自带的记事本将文件保存为UTF-8编码的时候，记事本会自动在文件开头插入BOM（虽然BOM对UTF-8来说并不是必须的）。而其它很多编辑器用不用BOM是可以选择的。UTF-8、UTF-16都是如此。</div>
<div></div>
<div>（3）BOM与XML</div>
<div></div>
<div>XML解析读取XML文档时，W3C定义了3条规则：</div>
<div></div>
<div>①如果文档中有BOM，就定义了文件编码；</div>
<div>②如果文档中没有BOM，就查看XML声明中的编码属性；</div>
<div>③如果上述两者都没有，就假定XML文档采用UTF-8编码。</div>
<div></div>
<div><strong>6.2 决定文本的字符集与编码</strong></div>
<div></div>
<div>软件通常有三种途径来决定文本的字符集和编码。</div>
<div></div>
<div>（1）对于Unicode文本最标准的途径是检测文本最开头的几个字节。如：</div>
<div></div>
<div>开头字节        Charset/encoding</div>
<div> EF BB BF　　　 UTF-8</div>
<div> FE FF　　　　　UTF-16/UCS-2, little endian(UTF-16LE)</div>
<div> FF FE　　　　　UTF-16/UCS-2, big endian(UTF-16BE)</div>
<div> FF FE 00 00　　UTF-32/UCS-4, little endian.</div>
<div> 00 00 FE FF　　UTF-32/UCS-4, big-endia</div>
<div></div>
<div>（2）采取一种比较安全的方式来决定字符集及其编码，那就是弹出一个对话框来请示用户。</div>
<div></div>
<div>然而MBCS文本（ANSI）没有这些位于开头的字符集标记，现在很多软件保存文本为Unicode时，可以选择是否保存这些位于开头的字符集标记。因此，软件不应该依赖于这种途径。这时，软件可以采取一种比较安全的方式来决定字符集及其编码，那就是弹出一个对话框来请示用户。</div>
<div></div>
<div>（3）采取自己“猜”的方法。</div>
<div></div>
<div>如果软件不想麻烦用户，或者它不方便向用户请示，那它只能采取自己“猜”的方法，软件可以根据整个文本的特征来猜测它可能属于哪个charset，这就很可能不准了。使用记事本打开那个“联通”文件就属于这种情况。（把原本属于ANSI编码的文件当成UTF-8处理，详细说明见：http://blog.csdn.net/omohe/archive/2007/05/29/1630186.aspx）</div>
<div></div>
<div><strong>6.3 记事本的几种编码</strong></div>
<div></div>
<div>（1）ANSI编码</div>
<div>记事本默认保存的编码格式是：ANSI，即本地操作系统默认的内码，简体中文一般为GB2312。这个怎么验证呢？用记事本保存后，使用EmEditor、EditPlus和UltraEdit之类的文本编辑器打开。推荐使用EmEditor，打开后，在又下角会显示编码：GB2312。</div>
<div></div>
<div>（2）Unicode编码</div>
<div>用记事本另存为时，编码选择“Unicode”，用EmEditor打开该文件，发现编码格式是：UTF-16LE+BOM（有签名）。用十六进制方式查看，发现开头两字节为：FF FE。这就是BOM。</div>
<div></div>
<div>（3）Unicode big endian</div>
<div>用记事本另存为时，编码选择“Unicode”，用EmEditor打开该文件，发现编码格式是：UTF-16BE+BOM（有签名）。用十六进制方式查看，发现开头两字节为：FE FF。这就是BOM。</div>
<div></div>
<div>（4）UTF-8</div>
<div>用记事本另存为时，编码选择“UTF-8”，用EmEditor打开该文件，发现编码格式是：UTF-8（有签名）。用十六进制方式查看，发现开头三个字节为：EF BB BF。这就是BOM。</div>
<h2>7、几种误解，以及乱码产生的原因和解决办法</h2>
<div><strong>7.1 误解一</strong></div>
<div></div>
<div>在将“字节串”转化成“UNICODE 字符串”时，比如在读取文本文件时，或者通过网络传输文本时，容易将“字节串”简单地作为单字节字符串，采用每“一个字节”就是“一个字符”的方法进行转化。</div>
<div></div>
<div>而实际上，在非英文的环境中，应该将“字节串”作为 ANSI 字符串，采用适当的编码来得到 UNICODE 字符串，有可能“多个字节”才能得到“一个字符”。</div>
<div></div>
<div>通常，一直在英文环境下做开发的程序员们，容易有这种误解。</div>
<div></div>
<div><strong>7.2 误解二</strong></div>
<div></div>
<div>在 DOS，Windows 98 等非 UNICODE 环境下，字符串都是以 ANSI 编码的字节形式存在的。这种以字节形式存在的字符串，必须知道是哪种编码才能被正确地使用。这使我们形成了一个惯性思维：“字符串的编码”。</div>
<div></div>
<div>当 UNICODE 被支持后，Java 中的 String 是以字符的“序号”来存储的，不是以“某种编码的字节”来存储的，因此已经不存在“字符串的编码”这个概念了。只有在“字符串”与“字节串”转化时，或者，将一个“字节串”当成一个 ANSI 字符串时，才有编码的概念。</div>
<div></div>
<div>不少的人都有这个误解。</div>
<div></div>
<div><strong>7.3 分析与解决</strong></div>
<div></div>
<div>第一种误解，往往是导致乱码产生的原因。第二种误解，往往导致本来容易纠正的乱码问题变得更复杂。</div>
<div></div>
<div>在这里，我们可以看到，其中所讲的“误解一”，即采用每“一个字节”就是“一个字符”的转化方法，实际上也就等同于采用 iso-8859-1 进行转化。因此，我们常常使用 bytes = string.getBytes("iso-8859-1") 来进行逆向操作，得到原始的“字节串”。然后再使用正确的 ANSI 编码，比如 string = new String(bytes, "GB2312")，来得到正确的“UNICODE 字符串”。</div>
<h2>8、参考与深入阅读学习资料</h2>
<div>8.1 <a href="http://www.regexlab.com/zh/encoding.htm" target="_blank">《字符，字节和编码》http://www.regexlab.com/zh/encoding.htm</a>（强烈推介）</div>
<div>8.2 <a href="http://blog.csdn.net/omohe/archive/2007/05/29/1630186.aspx" target="_blank">《关于编码: ascii(ansi), gb-2312, unicode, utf8》http://blog.csdn.net/omohe/archive/2007/05/29/1630186.aspx</a></div>
<div>8.3 <a href="http://hi.baidu.com/%D6%F0%C4%BE/blog/item/772c5944d5e77e8bb3b7dcab.html" target="_blank">《Ansi,UTF8,Unicode,ASCII编码的区别》http://hi.baidu.com/%D6%F0%C4%BE/blog/item/772c5944d5e77e8bb3b7dcab.html</a></div>
<div>8.4 <a href="http://baike.baidu.com/view/40801.htm" target="_blank">百度百科《Unicode》http://baike.baidu.com/view/40801.htm</a></div>
<div>8.5 <a href="http://zhidao.baidu.com/question/52532619.html?fr=ala0" target="_blank">《Unicode与UTF-8/UTF-16之间有啥联系或区别？》http://zhidao.baidu.com/question/52532619.html?fr=ala0</a></div>

编码系列1：<a href="http://sqh.me/blog/?p=263">编码那点事----历史进化</a>
编码系列2：<a href="http://sqh.me/blog/?p=273">字符编码详解 part1</a>
编码系列3：<a href="http://sqh.me/blog/?p=275">字符编码详解 part2</a>
