

为velocity新增加四个指令：block,override,extends,super

<font color='red>super为v3.9.3新增加指令</font'>

<h1>一.目的：</h1>

<ul><li>父模板页面定义好布局,子模板可以重定义布局中的部分内容<br>
</li><li>使模板可以实现类似"类"的继承关系,并不限继承层次<br>
<h1>二.继承概榄：</h1></li></ul>

<b>父模板：base.vm</b>
<pre><code>&lt;html&gt;  <br>
&lt;head&gt;  <br>
    #block("head")   <br>
        base_head_content   <br>
    #end   <br>
&lt;/head&gt;  <br>
&lt;body&gt;   <br>
    #block("body")   <br>
        base_body_content   <br>
    #end  <br>
&lt;/body&gt;  <br>
&lt;/html&gt;  <br>
</code></pre>

<b>子模板: child.vm</b>
<pre><code>#override("body")  <br>
    &lt;div class='content'&gt;  <br>
        Powered By rapid-framework  <br>
    &lt;/div&gt;<br>
    #super   <br>
#end  <br>
#extends("base.vm") <br>
</code></pre>

<b>子模板child.vm</b><font color='red'>输出</font>

<pre><code>&lt;html&gt;  <br>
    &lt;head&gt;  <br>
        base_head_content   <br>
    &lt;/head&gt;  <br>
&lt;body&gt;   <br>
    &lt;div class='content'&gt;  <br>
        Powered By rapid-framework  <br>
    &lt;/div&gt;<br>
    base_body_content   <br>
&lt;/body&gt;  <br>
&lt;/html&gt;  <br>
</code></pre>


可以看到，输出中body部分被子模板重定义(override)了，而head部分则还是显示父模板(block)中的内容。#super指令用于显示父模板的内容<br>
<br>
<br>
<h1>三.指令介绍:</h1>

<ul><li><b>#block :</b> 定义块，可以被子模板用#override指令覆盖显示<br>
</li><li><b>#override :</b>  覆盖#block指令显示的内容<br>
</li><li><b>#super :</b> 用于在override中显示父模板的内容,与java的super关键字功能类似.<font color='red>super为v3.9.3新增加指令</font'>
</li><li><b>#extends :</b> 继承其它模板，必须放在模板的最后面（注：该指令完全等价于#parse指令，只是为了提供统一的语义，即extends比parse更好理解）</li></ul>

<h1>四.使用说明:</h1>

在velocity的properties中可以设置:<br>
<pre><code>userdirective=cn.org.rapid_framework.velocity.directive.BlockDirective,cn.org.rapid_framework.velocity.directive.OverrideDirective,cn.org.rapid_framework.velocity.directive.ExtendsDirective,cn.org.rapid_framework.velocity.directive.SuperDirective<br>
</code></pre>


然后再初始化，即可使用自定义指令：<br>
<br>
<br>
<pre><code>velocityEngine.init(properties);  <br>
</code></pre>
<font color='red'>使用注意(性能优化):</font><b><blockquote>必须为velocity的ResourceLoader开启cache,因为 engine.getTemplate(name) 默认是没有使用cache的,而#extends是需要经常调用该函数,所以必须为ResourceLoader指定开启cache,如FileResourceLoader</blockquote></b>


<pre><code>file.resource.loader.cache = true  <br>
</code></pre>

<h1>五.源代码</h1>
只用主要的三个类即实现整个功能.<br>
<br>
<a href='http://rapid-framework.googlecode.com/svn/trunk/rapid-framework/src/rapid_framework_common/cn/org/rapid_framework/velocity/directive/'>http://rapid-framework.googlecode.com/svn/trunk/rapid-framework/src/rapid_framework_common/cn/org/rapid_framework/velocity/directive/</a>