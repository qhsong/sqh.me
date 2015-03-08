+++
Description = "在limesurvey提交表单之间加入自己需要的认证功能"
Tags = ["limesurvey","提交表单","修改"]
date = "2012-12-19T00:24:01+08:00"
title = "在limesurvey中加入自己想要的用户验证的功能"

+++
<p>最近这几天想要做一个调查，首选limesurvey，但是再升级到1.9X版本之后，发现当我多项选择题的时候，当我选择的选项超过我自己设定的个数的时候，他不会弹出提示，提示你选多了，而是只会在提交之后，告诉你说某个选项有答案不符合规则，要重新填写。这样来说，很不方便用户判断，还要再提交一次，也非常不爽。</p>

<p>经过几天代码的研究，首先发现，在input下是这样的一句话。</p>

```PHP
<input id="answer99766X2X21" 
onclick="cancelBubbleThis(event);checkconditions(this.value, this.name, this.type)" 
type="checkbox" name="99766X2X21" value="Y" >
```
<p>也就是说，他的每一个选项当点击的时候，都会有checkconditions(this.value, this.name, this.type)执行，我的选项都是多选题，所以type都是checkbox。这个毋庸置疑，但是在找checkconditions这个函数的时候颇费了一些功夫，最后使用DW的查找功能，把这个函数的原型给挖出来了。在group.php的第710行，定义如下：</p>

```PHP
echo "<input type='text' id='runonce' value='0' style='display: none;'/>
<!-- JAVASCRIPT FOR CONDITIONAL QUESTIONS -->
<script type='text/javascript'>
<!--\n";

echo "var LEMradix='" . $radix . "';\n";
echo "var numRegex = new RegExp('[^-' + LEMradix + '0-9]','g');\n";
echo "var intRegex = new RegExp('[^-0-9]','g');\n";
echo "var count=0;\n";
print <<<END
</pre>

<pre lang="javascript">
	function fixnum_checkconditions(value, name, type, evt_type, intonly)
	{
        newval = new String(value);
        if (typeof intonly !=='undefined' && intonly==1) {
            newval = newval.replace(intRegex,'');
        }
        else {
            newval = newval.replace(numRegex,'');
        }
        if (LEMradix === ',') {
            newval = newval.split(',').join('.');
        }
        if (newval != '-' && newval != '.' && newval != '-.' && newval != parseFloat(newval)) {
            newval = '';
        }
        displayVal = newval;
        if (LEMradix === ',') {
            displayVal = displayVal.split('.').join(',');
        }
        if (name.match(/other$/)) {
            $('#answer'+name+'text').val(displayVal);
        }
        $('#answer'+name).val(displayVal);

        if (typeof evt_type === 'undefined')
        {
            evt_type = 'onchange';
        }
        checkconditions(newval, name, type, evt_type);
	}

	function checkconditions(value, name, type, evt_type)
	{
		
        if (typeof evt_type === 'undefined')
        {
            evt_type = 'onchange';
        }
        if (type == 'radio' || type == 'select-one')
        {
            var hiddenformname='java'+name;
            document.getElementById(hiddenformname).value=value;
        }
        else if (type == 'checkbox')
        {
            if (document.getElementById('answer'+name).checked)
            {
                $('#java'+name).val('Y');				
            } else
            {
                $('#java'+name).val('');
            }
        }
        else if (type == 'text' && name.match(/other$/) && typeof document.getElementById('java'+name) !== 'undefined' && document.getElementById('java'+name) != null)
        {
            $('#java'+name).val(value);
        }
        ExprMgr_process_relevance_and_tailoring(evt_type,name,type);

END;
```

<p>这样，当我们确定名字和类型之后，完全可以通过checkconditions()这个函数进行修改，按照我自己的需要，增加自己的代码。我自己同时只会有一个调查在运行，所以我这样更改也没事，但是如果有多个调查要注意了，不能够影响其他的调查。最好用this.name的值来判断是否这个选项被选中。</p>
<p>好了，下面按照我的计划修改了，先定义一个全局变量，此刻的全局变量要用php代码echo出去：</p>
<pre lang="php">echo "var count=0;\n";</pre>
<p>然后再修改checkcondition部分，这个就按自己需求修改了。。。改天将自己的修改结果附后。</p>
<hr/>
<p>扯淡几句，唱歌很高兴。今天也不知道小晖生啥气了，不开心。生气。。。郁闷。晚安。</p>
