+++
Description = "多看英文书摘 调用ajax翻译"
Tags = ["多看","书摘","js","ajax"]
date = "2013-05-17T20:45:09+08:00"
title = "在电脑上将多看英文书摘统一翻译"

+++
最近在多看上看经济学人，不懂的单词就用多看的书摘记录下来。经过折腾发现多看的书摘是xml格式的，正好最近学了点ajex和xml，就干脆用上来，做个翻译。

1、多看的书摘保存在了DK_News文件夹里面，每本书一个文件夹，点进去之后就有两个文件dkpt和dkx，如图1所示。而dkx就是我们保存书签的xml文件，将它拷贝出来。

<img class="size-full wp-image-582 aligncenter" alt="duokan1" src="http://sqh.me/blog/wp-content/uploads/2013/05/duokan1.jpg" width="458" height="332" />

&nbsp;
<p style="text-align: center;">图一 多看文件夹示意图</p>
<p style="text-align: left;">2、用XMLNotepad 2007（一个专门查看xml文件的软件，微软开发的），dkx的目录是这样的：</p>
<p style="text-align: left;"><a href="http://sqh.me/blog/wp-content/uploads/2013/05/duokan2.jpg"><img class="size-full wp-image-583 aligncenter" alt="duokan2" src="http://sqh.me/blog/wp-content/uploads/2013/05/duokan2.jpg" width="605" height="513" /></a></p>
<p style="text-align: left;">我们书签的文字都放在了BookContent里面。</p>
<p style="text-align: left;">3、查词程序：之前搜索到一个dict.cn的API，还看到了微软bing词典的API，dict.cn感觉不是太好，微软的API又太麻烦，后来找到了有道的API，于是乎所有词典的翻译就来自有道了，具体见：<a href="http://fanyi.youdao.com/openapi?path=data-mode">http://fanyi.youdao.com/openapi?path=data-mode</a>。通过Get请求数据，然后使用Json返回数据。</p>
<p style="text-align: left;">4、网页
```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />
<style type="text/css">
    td{
        margin:5px 5px 3px 10px;
        text-align:center;
        }
    li{
        text-align:left;
    }
</style>

<script language="javascript">
    function a() {
    }
    function trans(string) {
        var xmlhttp;
        if (window.XMLHttpRequest) {
            xmlhttp = new XMLHttpRequest();
        } else {
            xmlhttp = new ActiveXObject("Mircosoft.XMLHTTP");
        }
        xmlhttp.open("GET", "http://fanyi.youdao.com/openapi.do?keyfrom=qhsongWeb&key=751043592&type=data&doctype=json&version=1.1&q=" + string, false);
        xmlhttp.send();
        var transMess = "";
        if (xmlhttp.responseText == 'no query') {
            transMess = "未找到单词";
        } else {
            var data = JSON.parse(xmlhttp.responseText);
            if (typeof (data.translation) != 'undefined')
                transMess += "<td>" + data.translation[0] + "    </td>";
                
            if (typeof (data.basic) != 'undefined') {
                transMess += "<td>"+data.basic.explains[0] + "     </td>";
            } else {
                transMess += "<td>" + "未找到" + "</td>";
            
            }
            if (typeof (data.web) != 'undefined') {
                transMess += "<td><ul>";
                for (j = 0; j < data.web.length;j++) {
                    transMess += "<li>" + data.web[j].key + "/" + data.web[j].value[0] + "</li>";
                }
                transMess += "</ul></td>";
            } else {
                transMess += "<td>" + "未找到" + "</td>";
            }
        }
        return transMess;
    }
    
function loadxml(){
	var xmlDoc;
	if(window.ActiveXObject){
		xmlDoc = new ActiveXObject('Microsoft.XMLDOM');
		xmlDoc.async = false;
		xmlDoc.load("duokan.xml");
	}
	else{
	    var xmlhttp = new XMLHttpRequest();
	    xmlhttp.open("get", "duokan.xml", false);
	    xmlhttp.send();
	    xmlDoc = xmlhttp.responseXML.documentElement;
	    xmlDoc.async="false";
	}
	return xmlDoc;
	}
	
	
function mfunc(){
	var xmlDoc= loadxml();
	var v = xmlDoc.documentElement.getElementsByTagName('BookContent');
	var list = "<table border='1'>";
	list +="<tr><td>单词</td><td>翻译</td><td>基本解释</td><td>相关词组</td></tr>"
	for (i = 0; i < v.length; i++) {
	    var y = v[i].text;
	    if (y == "") continue;
	    m=trans(y);
	    list = list + "<tr><td>" + y + "</td>" + m + "</tr>";
	}
	list +="</table>"
	document.getElementById("word").innerHTML = list;
	//alert(y);
	//document.write(v.childNodes[0].nodeValue);
	}
	
	
</script>
<title>无标题文档</title>
</head>

<body onload="mfunc()">
<div name="word" id="word">
</div>
</body>
</html>

```

这样，只要讲几个文件放在一起，稍等一会就回自动查词并显示了。我自己申请的api每个小时只能查1000次，所以请使用的同志手下留情，少查询点词语。由于chrome不能跨站ajax，并且xml不支持本地读取，所以暂时还不能使用，只能在IE下使用，以后有时间了把他折腾去我网站上动态更新去。

结果样子见下图，没什么美工，凑合用用：

<a href="http://sqh.me/blog/wp-content/uploads/2013/05/duokan3.jpg"><img class="size-medium wp-image-589 aligncenter" alt="duokan3" src="http://sqh.me/blog/wp-content/uploads/2013/05/duokan3-300x143.jpg" width="300" height="143" /></a>

&nbsp;

查询完了直接复制粘贴到excel，非常爽。

5、导出之后，到XMLNotepad中去，把原来的ReadingDataItem节点全删了，覆盖了原有的文件，这样又可以继续原来的接着阅读，原来的笔记已经被清空了。

6、整个文件打包下载：点击下载：<a href="http://sqh.me/blog/wp-content/uploads/2013/05/xml.rar">xml</a>

7、自己还是对Ajax和JS不熟悉，这点代码磨磨唧唧的从1点写到了晚上9点，我觉得我会被鄙视死的。觉得知识还是要有一个框架，像xml，自己拿过来现学现用，自己还是连框架都还没搞明白。DOM模型之类的。。。有机会还要学习。美工方面，还有看看能不能直接让大家上传文件，然后我生成了导出，没空折腾了。要看书了。这几天都没怎么看书，过几天就要考网络了。
<p style="text-align: right;">2013年5月17日20:43:03</p>
