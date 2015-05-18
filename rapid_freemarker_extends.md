

现扩展freemarker，新增加四个指令： @extends,@block,@override,@super

<font color='red>@super为v3.9.3新增加指令</font'>

<h1>一.目的</h1>
<ol><li>父模板页面定义好布局,子模板可以重定义布局中的部分内容<br>
</li><li>使模板可以实现类似"类"的继承关系,并不限继承层次</li></ol>

<h1>二.继承概榄</h1>

<b>父模板: base.ftl</b>

<pre><code>&lt;html&gt;  <br>
    &lt;head&gt;  <br>
        &lt;@block name="head"&gt;base_head_content&lt;/@block&gt;  <br>
    &lt;/head&gt;  <br>
    &lt;body&gt;  <br>
        &lt;@block name="body"&gt;base_body_content&lt;/@block&gt;  <br>
    &lt;/body&gt;  <br>
&lt;/html&gt;  <br>
</code></pre>

<b>子模板child.ftl</b>

<pre><code>&lt;@override name="body"&gt;  <br>
    &lt;div class='content'&gt;  <br>
        Powered By rapid-framework  <br>
    &lt;/div&gt;<br>
    &lt;@super/&gt;  <br>
&lt;/@override&gt;  <br>
&lt;@extends name="base.flt"/&gt;  <br>
</code></pre>

<b>子模板child.ftl</b><font color='red'>输出</font>

<pre><code>&lt;html&gt;  <br>
    &lt;head&gt;  <br>
        base_head_content  <br>
    &lt;/head&gt;  <br>
    &lt;body&gt;  <br>
        &lt;div class='content'&gt;  <br>
            Powered By rapid-framework  <br>
        &lt;/div&gt; <br>
        base_body_content <br>
    &lt;/body&gt;  <br>
&lt;/html&gt;  <br>
</code></pre>

<blockquote>可以看到，body部分被重定义了，而head部分则还是显示父模板的内容。</blockquote>

<h1>三.指令介绍</h1>

<ul><li>@block : 定义块，可以被子模板用@override指令覆盖显示,没有被override则显示自身的内容<br>
</li><li>@override :  重定义@block指令显示的内容<br>
</li><li>@super : 在override中显示 super block的内容,类似java的 super关键字 <font color='red>v3.9.3新增加指令</font'>
</li><li>@extends : 继承其它模板，必须放在模板的最后面（注：该指令完全等价于#include指令，只是为了提供统一的语义，即extends比include更好理解）</li></ul>

<b>@extends使用通配符</b>

如你有一个模板: /foo/bar/template.ftl<br>
<pre><code>&lt;@extends "*/footer.ftl"&gt;<br>
</code></pre>
freemarker模板引擎将自动查找:<br>
<ul><li>/foo/bar/footer.ftl<br>
</li><li>/foo/footer.ftl<br>
</li><li>/footer.ftl</li></ul>

完全参考请查看freemarker指令：<br>
<br>
<a href='http://freemarker.sourceforge.net/docs/ref_directive_include.html'>http://freemarker.sourceforge.net/docs/ref_directive_include.html</a>

<h1>四.使用说明</h1>

要使用如上自定义指令，必须在freeemarker的Configuration中注册。<br>
使用如下代码：<br>
<br>
<pre><code>configuration.setSharedVariable("block", new BlockDirective());  <br>
configuration.setSharedVariable("override", new OverrideDirective());  <br>
configuration.setSharedVariable("extends", new ExtendsDirective());  <br>
configuration.setSharedVariable("super", new SuperDirective())<br>
</code></pre>

如果你是使用springmvc,那么可以使用如下配置:<br>
<pre><code>&lt;bean id="blockDirective" class="cn.org.rapid_framework.freemarker.directive.BlockDirective"/&gt;<br>
&lt;bean id="extendsDirective" class="cn.org.rapid_framework.freemarker.directive.ExtendsDirective"/&gt;<br>
&lt;bean id="overrideDirective" class="cn.org.rapid_framework.freemarker.directive.OverrideDirective"/&gt;<br>
&lt;bean id="superDirective" class="cn.org.rapid_framework.freemarker.directive.SuperDirective"/&gt;<br>
<br>
&lt;!-- freemarker config for FreeMarkerViewResolver --&gt;<br>
&lt;bean id="freemarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer"&gt;<br>
	&lt;property name="templateLoaderPath" value="/freemarker/"/&gt;<br>
	&lt;property name="defaultEncoding" value="UTF-8"/&gt;<br>
	&lt;property name="freemarkerVariables"&gt;<br>
		&lt;map&gt;<br>
			&lt;entry key="extends" value-ref="extendsDirective"&gt;&lt;/entry&gt;<br>
			&lt;entry key="override" value-ref="overrideDirective"&gt;&lt;/entry&gt;<br>
			&lt;entry key="block" value-ref="blockDirective"&gt;&lt;/entry&gt;<br>
			&lt;entry key="super" value-ref="superDirective"&gt;&lt;/entry&gt;<br>
		&lt;/map&gt;<br>
	&lt;/property&gt;<br>
&lt;/bean&gt;<br>
</code></pre>


<h1>五.源代码</h1>

<a href='http://rapid-framework.googlecode.com/svn/trunk/rapid-framework/src/rapid_framework_common/cn/org/rapid_framework/freemarker/directive/'>http://rapid-framework.googlecode.com/svn/trunk/rapid-framework/src/rapid_framework_common/cn/org/rapid_framework/freemarker/directive/</a>

<h1>六.性能测试</h1>
由SeanHe提供的sitemesh,rapid性能测试比较 <a href='http://www.javaeye.com/topic/715100'>http://www.javaeye.com/topic/715100</a>