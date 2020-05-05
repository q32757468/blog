---
title: Chrome插件(扩展)开发全攻略
date: 2019-05-15 16:14:47
tags:
 - 转载
 - chrome
 - chrome插件开发
categories:
---
{% raw %}
<h1>写在前面</h1>
<p>这篇文章来自于转载，作者花了许多时间编写，写的也很详细，也很辛苦，我已经在文章尾部标明，也感谢作者的分享，<a id="cb_post_title_url" class="postTitle2" href="https://www.cnblogs.com/liuxianan/p/chrome-plugin-develop.html">这是文章原地址</a>以下是作者原文：来源我花了将近一个多月的时间断断续续写下这篇博文，并精心写下完整demo，写博客的辛苦大家懂的，所以转载务必保留<a href="http://blog.haoji.me/chrome-plugin-develop.html" target="_blank">出处</a>。本文所有涉及到的大部分代码均在这个demo里面：<a href="https://github.com/sxei/chrome-plugin-demo" class="uri" target="_blank">https://github.com/sxei/chrome-plugin-demo</a> ，大家可以直接下载下来运行。</p>
<!-- more -->
<p>另外，本文图片较多，且图片服务器带宽有限，右下角的目录滚动监听必须等到图片全部加载完毕之后才会触发，所以请耐心等待加载完毕。</p>
<p>本文目录：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711100228759-367621595.png">
<p>demo部分截图：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711100429118-918303767.png">
<h1 id="前言">前言</h1>
<h2 id="什么是chrome插件">2.1. 什么是Chrome插件</h2>
<p>严格来讲，我们正在说的东西应该叫Chrome扩展(<code>Chrome Extension</code>)，真正意义上的Chrome插件是更底层的浏览器功能扩展，可能需要对浏览器源码有一定掌握才有能力去开发。鉴于Chrome插件的叫法已经习惯，本文也全部采用这种叫法，但读者需深知本文所描述的Chrome插件实际上指的是Chrome扩展。</p>
<p>Chrome插件是一个用Web技术开发、用来增强浏览器功能的软件，它其实就是一个由HTML、CSS、JS、图片等资源组成的一个<a href="https://developer.chrome.com/extensions/crx" target="_blank">.crx</a>后缀的压缩包.</p>
<p>个人猜测<code>crx</code>可能是<code>Chrome Extension</code>如下3个字母的简写：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711100448275-555008903.png">
<p>另外，其实不只是前端技术，Chrome插件还可以配合C++编写的dll动态链接库实现一些更底层的功能(NPAPI)，比如全屏幕截图。</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711100549040-348646504.png">
<blockquote>
<p>由于安全原因，Chrome浏览器42以上版本已经陆续不再支持NPAPI插件，取而代之的是更安全的PPAPI。</p>
</blockquote>
<h2 id="学习chrome插件开发有什么意义">2.2. 学习Chrome插件开发有什么意义</h2>
<p>增强浏览器功能，轻松实现属于自己的“定制版”浏览器，等等。</p>
<p>Chrome插件提供了很多实用API供我们使用，包括但不限于：</p>
<ul>
<li>书签控制；</li>
<li>下载控制；</li>
<li>窗口控制；</li>
<li>标签控制；</li>
<li>网络请求控制，各类事件监听；</li>
<li>自定义原生菜单；</li>
<li>完善的通信机制；</li>
<li>等等；</li>
</ul>
<h2 id="为什么是chrome插件而不是firefox插件">2.3. 为什么是Chrome插件而不是Firefox插件</h2>
<ol>
<li>Chrome占有率更高，更多人用；</li>
<li>开发更简单；</li>
<li>应用场景更广泛，Firefox插件只能运行在Firefox上，而Chrome除了Chrome浏览器之外，还可以运行在所有webkit内核的国产浏览器，比如360极速浏览器、360安全浏览器、搜狗浏览器、QQ浏览器等等；</li>
<li>除此之外，Firefox浏览器也对Chrome插件的运行提供了一定的支持；</li>
</ol>
<h1 id="开发与调试">开发与调试</h1>
<p>Chrome插件没有严格的项目结构要求，只要保证本目录有一个<code>manifest.json</code>即可，也不需要专门的IDE，普通的web开发工具即可。</p>
<p>从右上角菜单-&gt;更多工具-&gt;扩展程序可以进入 插件管理页面，也可以直接在地址栏输入 <a href="chrome://extensions/" target="_blank">chrome://extensions</a> 访问。</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101025931-1366345527.png">
<p>勾选<code>开发者模式</code>即可以文件夹的形式直接加载插件，否则只能安装<code>.crx</code>格式的文件。Chrome要求插件必须从它的Chrome应用商店安装，其它任何网站下载的都无法直接安装，所以，其实我们可以把<code>crx</code>文件解压，然后通过开发者模式直接加载。</p>
<p>开发中，代码有任何改动都必须重新加载插件，只需要在插件管理页按下<code>Ctrl+R</code>即可，以防万一最好还把页面刷新一下。</p>
<h1 id="核心介绍">核心介绍</h1>
<h2 id="manifest.json">4.1. manifest.json</h2>
<p>这是一个Chrome插件最重要也是必不可少的文件，用来配置所有和插件相关的配置，必须放在根目录。其中，<code>manifest_version</code>、<code>name</code>、<code>version</code>3个是必不可少的，<code>description</code>和<code>icons</code>是推荐的。</p>
<p>下面给出的是一些常见的配置项，均有中文注释，完整的配置文档请戳<a href="https://developer.chrome.com/extensions/manifest" target="_blank">这里</a>。</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-comment">// 清单文件的版本，这个必须写，而且必须是2</span>
    <span class="hljs-string">"manifest_version"</span>: <span class="hljs-number">2</span>,
    <span class="hljs-comment">// 插件的名称</span>
    <span class="hljs-string">"name"</span>: <span class="hljs-string">"demo"</span>,
    <span class="hljs-comment">// 插件的版本</span>
    <span class="hljs-string">"version"</span>: <span class="hljs-string">"1.0.0"</span>,
    <span class="hljs-comment">// 插件描述</span>
    <span class="hljs-string">"description"</span>: <span class="hljs-string">"简单的Chrome扩展demo"</span>,
    <span class="hljs-comment">// 图标，一般偷懒全部用一个尺寸的也没问题</span>
    <span class="hljs-string">"icons"</span>:
    {
        <span class="hljs-string">"16"</span>: <span class="hljs-string">"img/icon.png"</span>,
        <span class="hljs-string">"48"</span>: <span class="hljs-string">"img/icon.png"</span>,
        <span class="hljs-string">"128"</span>: <span class="hljs-string">"img/icon.png"</span>
    },
    <span class="hljs-comment">// 会一直常驻的后台JS或后台页面</span>
    <span class="hljs-string">"background"</span>:
    {
        <span class="hljs-comment">// 2种指定方式，如果指定JS，那么会自动生成一个背景页</span>
        <span class="hljs-string">"page"</span>: <span class="hljs-string">"background.html"</span>
        <span class="hljs-comment">//"scripts": ["js/background.js"]</span>
    },
    <span class="hljs-comment">// 浏览器右上角图标设置，browser_action、page_action、app必须三选一</span>
    <span class="hljs-string">"browser_action"</span>: 
    {
        <span class="hljs-string">"default_icon"</span>: <span class="hljs-string">"img/icon.png"</span>,
        <span class="hljs-comment">// 图标悬停时的标题，可选</span>
        <span class="hljs-string">"default_title"</span>: <span class="hljs-string">"这是一个示例Chrome插件"</span>,
        <span class="hljs-string">"default_popup"</span>: <span class="hljs-string">"popup.html"</span>
    },
    <span class="hljs-comment">// 当某些特定页面打开才显示的图标</span>
    <span class="hljs-comment">/*"page_action":
    {
        "default_icon": "img/icon.png",
        "default_title": "我是pageAction",
        "default_popup": "popup.html"
    },*/</span>
    <span class="hljs-comment">// 需要直接注入页面的JS</span>
    <span class="hljs-string">"content_scripts"</span>: 
    [
        {
            <span class="hljs-comment">//"matches": ["http://*/*", "https://*/*"],</span>
            <span class="hljs-comment">// "&lt;all_urls&gt;" 表示匹配所有地址</span>
            <span class="hljs-string">"matches"</span>: [<span class="hljs-string">"&lt;all_urls&gt;"</span>],
            <span class="hljs-comment">// 多个JS按顺序注入</span>
            <span class="hljs-string">"js"</span>: [<span class="hljs-string">"js/jquery-1.8.3.js"</span>, <span class="hljs-string">"js/content-script.js"</span>],
            <span class="hljs-comment">// JS的注入可以随便一点，但是CSS的注意就要千万小心了，因为一不小心就可能影响全局样式</span>
            <span class="hljs-string">"css"</span>: [<span class="hljs-string">"css/custom.css"</span>],
            <span class="hljs-comment">// 代码注入的时间，可选值： "document_start", "document_end", or "document_idle"，最后一个表示页面空闲时，默认document_idle</span>
            <span class="hljs-string">"run_at"</span>: <span class="hljs-string">"document_start"</span>
        },
        <span class="hljs-comment">// 这里仅仅是为了演示content-script可以配置多个规则</span>
        {
            <span class="hljs-string">"matches"</span>: [<span class="hljs-string">"*://*/*.png"</span>, <span class="hljs-string">"*://*/*.jpg"</span>, <span class="hljs-string">"*://*/*.gif"</span>, <span class="hljs-string">"*://*/*.bmp"</span>],
            <span class="hljs-string">"js"</span>: [<span class="hljs-string">"js/show-image-content-size.js"</span>]
        }
    ],
    <span class="hljs-comment">// 权限申请</span>
    <span class="hljs-string">"permissions"</span>:
    [
        <span class="hljs-string">"contextMenus"</span>, <span class="hljs-comment">// 右键菜单</span>
        <span class="hljs-string">"tabs"</span>, <span class="hljs-comment">// 标签</span>
        <span class="hljs-string">"notifications"</span>, <span class="hljs-comment">// 通知</span>
        <span class="hljs-string">"webRequest"</span>, <span class="hljs-comment">// web请求</span>
        <span class="hljs-string">"webRequestBlocking"</span>,
        <span class="hljs-string">"storage"</span>, <span class="hljs-comment">// 插件本地存储</span>
        <span class="hljs-string">"http://*/*"</span>, <span class="hljs-comment">// 可以通过executeScript或者insertCSS访问的网站</span>
        <span class="hljs-string">"https://*/*"</span> <span class="hljs-comment">// 可以通过executeScript或者insertCSS访问的网站</span>
    ],
    <span class="hljs-comment">// 普通页面能够直接访问的插件资源列表，如果不设置是无法直接访问的</span>
    <span class="hljs-string">"web_accessible_resources"</span>: [<span class="hljs-string">"js/inject.js"</span>],
    <span class="hljs-comment">// 插件主页，这个很重要，不要浪费了这个免费广告位</span>
    <span class="hljs-string">"homepage_url"</span>: <span class="hljs-string">"https://www.baidu.com"</span>,
    <span class="hljs-comment">// 覆盖浏览器默认页面</span>
    <span class="hljs-string">"chrome_url_overrides"</span>:
    {
        <span class="hljs-comment">// 覆盖浏览器默认的新标签页</span>
        <span class="hljs-string">"newtab"</span>: <span class="hljs-string">"newtab.html"</span>
    },
    <span class="hljs-comment">// Chrome40以前的插件配置页写法</span>
    <span class="hljs-string">"options_page"</span>: <span class="hljs-string">"options.html"</span>,
    <span class="hljs-comment">// Chrome40以后的插件配置页写法，如果2个都写，新版Chrome只认后面这一个</span>
    <span class="hljs-string">"options_ui"</span>:
    {
        <span class="hljs-string">"page"</span>: <span class="hljs-string">"options.html"</span>,
        <span class="hljs-comment">// 添加一些默认的样式，推荐使用</span>
        <span class="hljs-string">"chrome_style"</span>: <span class="hljs-literal">true</span>
    },
    <span class="hljs-comment">// 向地址栏注册一个关键字以提供搜索建议，只能设置一个关键字</span>
    <span class="hljs-string">"omnibox"</span>: { <span class="hljs-string">"keyword"</span> : <span class="hljs-string">"go"</span> },
    <span class="hljs-comment">// 默认语言</span>
    <span class="hljs-string">"default_locale"</span>: <span class="hljs-string">"zh_CN"</span>,
    <span class="hljs-comment">// devtools页面入口，注意只能指向一个HTML文件，不能是JS文件</span>
    <span class="hljs-string">"devtools_page"</span>: <span class="hljs-string">"devtools.html"</span>
}</code></pre>
<h2 id="content-scripts">4.2. content-scripts</h2>
<p>所谓<a href="https://developer.chrome.com/extensions/content_scripts" target="_blank">content-scripts</a>，其实就是Chrome插件中向页面注入脚本的一种形式（虽然名为script，其实还可以包括css的），借助<code>content-scripts</code>我们可以实现通过配置的方式轻松向指定页面注入JS和CSS（如果需要动态注入，可以参考下文），最常见的比如：广告屏蔽、页面CSS定制，等等。</p>
<p>示例配置：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-comment">// 需要直接注入页面的JS</span>
    <span class="hljs-string">"content_scripts"</span>: 
    [
        {
            <span class="hljs-comment">//"matches": ["http://*/*", "https://*/*"],</span>
            <span class="hljs-comment">// "&lt;all_urls&gt;" 表示匹配所有地址</span>
            <span class="hljs-string">"matches"</span>: [<span class="hljs-string">"&lt;all_urls&gt;"</span>],
            <span class="hljs-comment">// 多个JS按顺序注入</span>
            <span class="hljs-string">"js"</span>: [<span class="hljs-string">"js/jquery-1.8.3.js"</span>, <span class="hljs-string">"js/content-script.js"</span>],
            <span class="hljs-comment">// JS的注入可以随便一点，但是CSS的注意就要千万小心了，因为一不小心就可能影响全局样式</span>
            <span class="hljs-string">"css"</span>: [<span class="hljs-string">"css/custom.css"</span>],
            <span class="hljs-comment">// 代码注入的时间，可选值： "document_start", "document_end", or "document_idle"，最后一个表示页面空闲时，默认document_idle</span>
            <span class="hljs-string">"run_at"</span>: <span class="hljs-string">"document_start"</span>
        }
    ],
}</code></pre>
<p>特别注意，如果没有主动指定<code>run_at</code>为<code>document_start</code>（默认为<code>document_idle</code>），下面这种代码是不会生效的：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-built_in">document</span>.addEventListener(<span class="hljs-string">'DOMContentLoaded'</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)
</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'我被执行了！'</span>);
});</code></pre>
<p><code>content-scripts</code>和原始页面共享DOM，但是不共享JS，如要访问页面JS（例如某个JS变量），只能通过<code>injected js</code>来实现。<code>content-scripts</code>不能访问绝大部分<code>chrome.xxx.api</code>，除了下面这4种：</p>
<ul>
<li>chrome.extension(getURL , inIncognitoContext , lastError , onRequest , sendRequest)</li>
<li>chrome.i18n</li>
<li>chrome.runtime(connect , getManifest , getURL , id , onConnect , onMessage , sendMessage)</li>
<li>chrome.storage</li>
</ul>
<p>其实看到这里不要悲观，这些API绝大部分时候都够用了，非要调用其它API的话，你还可以通过通信来实现让background来帮你调用（关于通信，后文有详细介绍）。</p>
<p>好了，Chrome插件给我们提供了这么强大的JS注入功能，剩下的就是发挥你的想象力去玩弄浏览器了。</p>
<h2 id="background">4.3. background</h2>
<p>后台（姑且这么翻译吧），是一个常驻的页面，它的生命周期是插件中所有类型页面中最长的，它随着浏览器的打开而打开，随着浏览器的关闭而关闭，所以通常把需要一直运行的、启动就运行的、全局的代码放在background里面。</p>
<p>background的权限非常高，几乎可以调用所有的Chrome扩展API（除了devtools），而且它可以无限制跨域，也就是可以跨域访问任何网站而无需要求对方设置<code>CORS</code>。</p>
<blockquote>
<p>经过测试，其实不止是background，所有的直接通过<code>chrome-extension://id/xx.html</code>这种方式打开的网页都可以无限制跨域。</p>
</blockquote>
<p>配置中，<code>background</code>可以通过<code>page</code>指定一张网页，也可以通过<code>scripts</code>直接指定一个JS，Chrome会自动为这个JS生成一个默认的网页：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-comment">// 会一直常驻的后台JS或后台页面</span>
    <span class="hljs-string">"background"</span>:
    {
        <span class="hljs-comment">// 2种指定方式，如果指定JS，那么会自动生成一个背景页</span>
        <span class="hljs-string">"page"</span>: <span class="hljs-string">"background.html"</span>
        <span class="hljs-comment">//"scripts": ["js/background.js"]</span>
    },
}</code></pre>
<p>需要特别说明的是，虽然你可以通过<code>chrome-extension://xxx/background.html</code>直接打开后台页，但是你打开的后台页和真正一直在后台运行的那个页面不是同一个，换句话说，你可以打开无数个<code>background.html</code>，但是真正在后台常驻的只有一个，而且这个你永远看不到它的界面，只能调试它的代码。</p>
<h2 id="event-pages">4.4. event-pages</h2>
<p>这里顺带介绍一下<a href="https://developer.chrome.com/extensions/event_pages" target="_blank">event-pages</a>，它是一个什么东西呢？鉴于background生命周期太长，长时间挂载后台可能会影响性能，所以Google又弄一个<code>event-pages</code>，在配置文件上，它与background的唯一区别就是多了一个<code>persistent</code>参数：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-string">"background"</span>:
    {
        <span class="hljs-string">"scripts"</span>: [<span class="hljs-string">"event-page.js"</span>],
        <span class="hljs-string">"persistent"</span>: <span class="hljs-literal">false</span>
    },
}</code></pre>
<p>它的生命周期是：在被需要时加载，在空闲时被关闭，什么叫被需要时呢？比如第一次安装、插件更新、有content-script向它发送消息，等等。</p>
<p>除了配置文件的变化，代码上也有一些细微变化，个人这个简单了解一下就行了，一般情况下background也不会很消耗性能的。</p>
<h2 id="popup">4.5. popup</h2>
<p><code>popup</code>是点击<code>browser_action</code>或者<code>page_action</code>图标时打开的一个小窗口网页，焦点离开网页就立即关闭，一般用来做一些临时性的交互。</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101054353-176942304.png" alt="博客园网摘插件popup效果"></p>
<p><code>popup</code>可以包含任意你想要的HTML内容，并且会自适应大小。可以通过<code>default_popup</code>字段来指定popup页面，也可以调用<code>setPopup()</code>方法。</p>
<p>配置方式：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-string">"browser_action"</span>:
    {
        <span class="hljs-string">"default_icon"</span>: <span class="hljs-string">"img/icon.png"</span>,
        <span class="hljs-comment">// 图标悬停时的标题，可选</span>
        <span class="hljs-string">"default_title"</span>: <span class="hljs-string">"这是一个示例Chrome插件"</span>,
        <span class="hljs-string">"default_popup"</span>: <span class="hljs-string">"popup.html"</span>
    }
}</code></pre>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101114415-2019243064.png">
<p>需要特别注意的是，由于单击图标打开popup，焦点离开又立即关闭，所以popup页面的生命周期一般很短，需要长时间运行的代码千万不要写在popup里面。</p>
<p>在权限上，它和background非常类似，它们之间最大的不同是生命周期的不同，popup中可以直接通过<code>chrome.extension.getBackgroundPage()</code>获取background的window对象。</p>
<h2 id="injected-script">4.6. injected-script</h2>
<p>这里的<code>injected-script</code>是我给它取的，指的是通过DOM操作的方式向页面注入的一种JS。为什么要把这种JS单独拿出来讨论呢？又或者说为什么需要通过这种方式注入JS呢？</p>
<p>这是因为<code>content-script</code>有一个很大的“缺陷”，也就是无法访问页面中的JS，虽然它可以操作DOM，但是DOM却不能调用它，也就是无法在DOM中通过绑定事件的方式调用<code>content-script</code>中的代码（包括直接写<code>onclick</code>和<code>addEventListener</code>2种方式都不行），但是，“在页面上添加一个按钮并调用插件的扩展API”是一个很常见的需求，那该怎么办呢？其实这就是本小节要讲的。</p>
<p>在<code>content-script</code>中通过DOM方式向页面注入<code>inject-script</code>代码示例：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 向页面注入JS</span>
<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">injectCustomJs</span>(<span class="hljs-params">jsPath</span>)
</span>{
    jsPath = jsPath || <span class="hljs-string">'js/inject.js'</span>;
    <span class="hljs-keyword">var</span> temp = <span class="hljs-built_in">document</span>.createElement(<span class="hljs-string">'script'</span>);
    temp.setAttribute(<span class="hljs-string">'type'</span>, <span class="hljs-string">'text/javascript'</span>);
    <span class="hljs-comment">// 获得的地址类似：chrome-extension://ihcokhadfjfchaeagdoclpnjdiokfakg/js/inject.js</span>
    temp.src = chrome.extension.getURL(jsPath);
    temp.onload = <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)
    </span>{
        <span class="hljs-comment">// 放在页面不好看，执行完后移除掉</span>
        <span class="hljs-keyword">this</span>.parentNode.removeChild(<span class="hljs-keyword">this</span>);
    };
    <span class="hljs-built_in">document</span>.head.appendChild(temp);
}</code></pre>
<p>你以为这样就行了？执行一下你会看到如下报错：</p>
<pre><code class="hljs sql">Denying <span class="hljs-keyword">load</span> <span class="hljs-keyword">of</span> chrome-extension://efbllncjkjiijkppagepehoekjojdclc/js/inject.js. Resources must be listed <span class="hljs-keyword">in</span> the web_accessible_resources manifest <span class="hljs-keyword">key</span> <span class="hljs-keyword">in</span> <span class="hljs-keyword">order</span> <span class="hljs-keyword">to</span> be loaded <span class="hljs-keyword">by</span> pages outside the extension.</code></pre>
<p>意思就是你想要在web中直接访问插件中的资源的话必须显示声明才行，配置文件中增加如下：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-comment">// 普通页面能够直接访问的插件资源列表，如果不设置是无法直接访问的</span>
    <span class="hljs-string">"web_accessible_resources"</span>: [<span class="hljs-string">"js/inject.js"</span>],
}</code></pre>
<p>至于<code>inject-script</code>如何调用<code>content-script</code>中的代码，后面我会在专门的一个消息通信章节详细介绍。</p>
<h2 id="homepage_url">4.7. homepage_url</h2>
<p>开发者或者插件主页设置，一般会在如下2个地方显示：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101138368-1160396252.png">
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101153556-956484967.png">
<h1 id="chrome插件的8种展示形式">Chrome插件的8种展示形式</h1>
<h2 id="browseraction浏览器右上角">5.1. browserAction(浏览器右上角)</h2>
<p>通过配置<code>browser_action</code>可以在浏览器的右上角增加一个图标，一个<code>browser_action</code>可以拥有一个图标，一个<code>tooltip</code>，一个<code>badge</code>和一个<code>popup</code>。</p>
<p>示例配置如下：</p>
<pre class="json"><code class="hljs"><span class="hljs-string">"browser_action"</span>:
{
    <span class="hljs-attr">"default_icon"</span>: <span class="hljs-string">"img/icon.png"</span>,
    <span class="hljs-attr">"default_title"</span>: <span class="hljs-string">"这是一个示例Chrome插件"</span>,
    <span class="hljs-attr">"default_popup"</span>: <span class="hljs-string">"popup.html"</span>
}</code></pre>
<h3 id="图标">5.1.1. 图标</h3>
<p><code>browser_action</code>图标推荐使用宽高都为19像素的图片，更大的图标会被缩小，格式随意，一般推荐png，可以通过manifest中<code>default_icon</code>字段配置，也可以调用setIcon()方法。</p>
<h3 id="tooltip">5.1.2. tooltip</h3>
<p>修改<code>browser_action</code>的manifest中<code>default_title</code>字段，或者调用<code>setTitle()</code>方法。</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101210759-683039077.png">
<h3 id="badge">5.1.3. badge</h3>
<p>所谓<code>badge</code>就是在图标上显示一些文本，可以用来更新一些小的扩展状态提示信息。因为badge空间有限，所以只支持4个以下的字符（英文4个，中文2个）。badge无法通过配置文件来指定，必须通过代码实现，设置badge文字和颜色可以分别使用<code>setBadgeText()</code>和<code>setBadgeBackgroundColor()</code>。</p>
<pre class="javascript"><code class="hljs">chrome.browserAction.setBadgeText({<span class="hljs-attr">text</span>: <span class="hljs-string">'new'</span>});
chrome.browserAction.setBadgeBackgroundColor({<span class="hljs-attr">color</span>: [<span class="hljs-number">255</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">255</span>]});</code></pre>
<p>效果：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101228056-2133169218.png">
<h2 id="pageaction地址栏右侧">5.2. pageAction(地址栏右侧)</h2>
<p>所谓<code>pageAction</code>，指的是只有当某些特定页面打开才显示的图标，它和<code>browserAction</code>最大的区别是一个始终都显示，一个只在特定情况才显示。</p>
<p>需要特别说明的是早些版本的Chrome是将pageAction放在地址栏的最右边，左键单击弹出popup，右键单击则弹出相关默认的选项菜单：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101549665-519093069.png">
<p>而新版的Chrome更改了这一策略，pageAction和普通的browserAction一样也是放在浏览器右上角，只不过没有点亮时是灰色的，点亮了才是彩色的，灰色时无论左键还是右键单击都是弹出选项：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101604947-1619892598.gif"></p>
<blockquote>
<p>具体是从哪一版本开始改的没去仔细考究，反正知道v50.0的时候还是前者，v58.0的时候已改为后者。</p>
</blockquote>
<p>调整之后的<code>pageAction</code>我们可以简单地把它看成是可以置灰的<code>browserAction</code>。</p>
<ul>
<li>chrome.pageAction.show(tabId) 显示图标；</li>
<li>chrome.pageAction.hide(tabId) 隐藏图标；</li>
</ul>
<p>示例(只有打开百度才显示图标)：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// manifest.json</span>
{
    <span class="hljs-string">"page_action"</span>:
    {
        <span class="hljs-string">"default_icon"</span>: <span class="hljs-string">"img/icon.png"</span>,
        <span class="hljs-string">"default_title"</span>: <span class="hljs-string">"我是pageAction"</span>,
        <span class="hljs-string">"default_popup"</span>: <span class="hljs-string">"popup.html"</span>
    },
    <span class="hljs-string">"permissions"</span>: [<span class="hljs-string">"declarativeContent"</span>]
}

<span class="hljs-comment">// background.js</span>
chrome.runtime.onInstalled.addListener(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
    chrome.declarativeContent.onPageChanged.removeRules(<span class="hljs-literal">undefined</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
        chrome.declarativeContent.onPageChanged.addRules([
            {
                <span class="hljs-attr">conditions</span>: [
                    <span class="hljs-comment">// 只有打开百度才显示pageAction</span>
                    <span class="hljs-keyword">new</span> chrome.declarativeContent.PageStateMatcher({<span class="hljs-attr">pageUrl</span>: {<span class="hljs-attr">urlContains</span>: <span class="hljs-string">'baidu.com'</span>}})
                ],
                <span class="hljs-attr">actions</span>: [<span class="hljs-keyword">new</span> chrome.declarativeContent.ShowPageAction()]
            }
        ]);
    });
});</code></pre>
<p>效果图：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101633728-1835819108.gif"></p>
<h2 id="右键菜单">5.3. 右键菜单</h2>
<p>通过开发Chrome插件可以自定义浏览器的右键菜单，主要是通过<code>chrome.contextMenus</code>API实现，右键菜单可以出现在不同的上下文，比如普通页面、选中的文字、图片、链接，等等，如果有同一个插件里面定义了多个菜单，Chrome会自动组合放到以插件名字命名的二级菜单里，如下：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101651618-268310284.png">
<h3 id="最简单的右键菜单示例">5.3.1. 最简单的右键菜单示例</h3>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// manifest.json</span>
{<span class="hljs-string">"permissions"</span>: [<span class="hljs-string">"contextMenus"</span>]}

<span class="hljs-comment">// background.js</span>
chrome.contextMenus.create({
    <span class="hljs-attr">title</span>: <span class="hljs-string">"测试右键菜单"</span>,
    <span class="hljs-attr">onclick</span>: <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{alert(<span class="hljs-string">'您点击了右键菜单！'</span>);}
});</code></pre>
<p>效果：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101704775-1000780193.png">
<h3 id="添加右键百度搜索">5.3.2. 添加右键百度搜索</h3>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// manifest.json</span>
{<span class="hljs-string">"permissions"</span>: [<span class="hljs-string">"contextMenus"</span>， <span class="hljs-string">"tabs"</span>]}

<span class="hljs-comment">// background.js</span>
chrome.contextMenus.create({
    <span class="hljs-attr">title</span>: <span class="hljs-string">'使用度娘搜索：%s'</span>, <span class="hljs-comment">// %s表示选中的文字</span>
    contexts: [<span class="hljs-string">'selection'</span>], <span class="hljs-comment">// 只有当选中文字时才会出现此右键菜单</span>
    onclick: <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">params</span>)
    </span>{
        <span class="hljs-comment">// 注意不能使用location.href，因为location是属于background的window对象</span>
        chrome.tabs.create({<span class="hljs-attr">url</span>: <span class="hljs-string">'https://www.baidu.com/s?ie=utf-8&amp;wd='</span> + <span class="hljs-built_in">encodeURI</span>(params.selectionText)});
    }
});</code></pre>
<p>效果如下：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101721384-916526079.png">
<h3 id="语法说明">5.3.3. 语法说明</h3>
<p>这里只是简单列举一些常用的，完整API参见：<a href="https://developer.chrome.com/extensions/contextMenus" class="uri" target="_blank">https://developer.chrome.com/extensions/contextMenus</a></p>
<pre class="javascript"><code class="hljs">chrome.contextMenus.create({
    <span class="hljs-attr">type</span>: <span class="hljs-string">'normal'</span>， <span class="hljs-comment">// 类型，可选：["normal", "checkbox", "radio", "separator"]，默认 normal</span>
    title: <span class="hljs-string">'菜单的名字'</span>, <span class="hljs-comment">// 显示的文字，除非为“separator”类型否则此参数必需，如果类型为“selection”，可以使用%s显示选定的文本</span>
    contexts: [<span class="hljs-string">'page'</span>], <span class="hljs-comment">// 上下文环境，可选：["all", "page", "frame", "selection", "link", "editable", "image", "video", "audio"]，默认page</span>
    onclick: <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{}, <span class="hljs-comment">// 单击时触发的方法</span>
    parentId: <span class="hljs-number">1</span>, <span class="hljs-comment">// 右键菜单项的父菜单项ID。指定父菜单项将会使此菜单项成为父菜单项的子菜单</span>
    documentUrlPatterns: <span class="hljs-string">'https://*.baidu.com/*'</span> <span class="hljs-comment">// 只在某些页面显示此右键菜单</span>
});
<span class="hljs-comment">// 删除某一个菜单项</span>
chrome.contextMenus.remove(menuItemId)；
<span class="hljs-comment">// 删除所有自定义右键菜单</span>
chrome.contextMenus.removeAll();
<span class="hljs-comment">// 更新某一个菜单项</span>
chrome.contextMenus.update(menuItemId, updateProperties);</code></pre>
<h2 id="override覆盖特定页面">5.4. override(覆盖特定页面)</h2>
<p>使用<code>override</code>页可以将Chrome默认的一些特定页面替换掉，改为使用扩展提供的页面。</p>
<p>扩展可以替代如下页面：</p>
<ul>
<li>历史记录：从工具菜单上点击历史记录时访问的页面，或者从地址栏直接输入 <a href="chrome://history" class="uri" target="_blank">chrome://history</a></li>
<li>新标签页：当创建新标签的时候访问的页面，或者从地址栏直接输入 <a href="chrome://newtab" class="uri" target="_blank">chrome://newtab</a></li>
<li>书签：浏览器的书签，或者直接输入 <a href="chrome://bookmarks" class="uri" target="_blank">chrome://bookmarks</a></li>
</ul>
<p>注意：</p>
<ul>
<li>一个扩展只能替代一个页面；</li>
<li>不能替代隐身窗口的新标签页；</li>
<li>网页必须设置title，否则用户可能会看到网页的URL，造成困扰；</li>
</ul>
<p>下面的截图是默认的新标签页和被扩展替换掉的新标签页。</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101740947-1059479610.png">
<p>代码（注意，一个插件只能替代一个默认页，以下仅为演示）：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-string">"chrome_url_overrides"</span>:
{
    <span class="hljs-string">"newtab"</span>: <span class="hljs-string">"newtab.html"</span>,
    <span class="hljs-string">"history"</span>: <span class="hljs-string">"history.html"</span>,
    <span class="hljs-string">"bookmarks"</span>: <span class="hljs-string">"bookmarks.html"</span>
}</code></pre>
<h2 id="devtools开发者工具">5.5. devtools(开发者工具)</h2>
<h3 id="预热">5.5.1. 预热</h3>
<p>使用过vue的应该见过这种类型的插件：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101756868-1307680533.png">
<p>是的，Chrome允许插件在开发者工具(devtools)上动手脚，主要表现在：</p>
<ul>
<li>自定义一个和多个和<code>Elements</code>、<code>Console</code>、<code>Sources</code>等同级别的面板；</li>
<li>自定义侧边栏(sidebar)，目前只能自定义<code>Elements</code>面板的侧边栏；</li>
</ul>
<p>先来看2张简单的demo截图，自定义面板（判断当前页面是否使用了jQuery）：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101815243-1381068889.png">
<p>自定义侧边栏（获取当前页面所有图片）：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101832259-552804777.png">
<h3 id="devtools扩展介绍">5.5.2. devtools扩展介绍</h3>
<p>主页：<a href="https://developer.chrome.com/extensions/devtools" class="uri" target="_blank">https://developer.chrome.com/extensions/devtools</a></p>
<p>来一张官方图片：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101847493-273760238.png">
<p>每打开一个开发者工具窗口，都会创建devtools页面的实例，F12窗口关闭，页面也随着关闭，所以devtools页面的生命周期和devtools窗口是一致的。devtools页面可以访问一组特有的<code>DevTools API</code>以及有限的扩展API，这组特有的<code>DevTools API</code>只有devtools页面才可以访问，background都无权访问，这些API包括：</p>
<ul>
<li><code>chrome.devtools.panels</code>：面板相关；</li>
<li><code>chrome.devtools.inspectedWindow</code>：获取被审查窗口的有关信息；</li>
<li><code>chrome.devtools.network</code>：获取有关网络请求的信息；</li>
</ul>
<p>大部分扩展API都无法直接被<code>DevTools</code>页面调用，但它可以像<code>content-script</code>一样直接调用<code>chrome.extension</code>和<code>chrome.runtime</code>API，同时它也可以像<code>content-script</code>一样使用Message交互的方式与background页面进行通信。</p>
<h3 id="实例创建一个devtools扩展">5.5.3. 实例：创建一个devtools扩展</h3>
<p>首先，要针对开发者工具开发插件，需要在清单文件声明如下：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-comment">// 只能指向一个HTML文件，不能是JS文件</span>
    <span class="hljs-string">"devtools_page"</span>: <span class="hljs-string">"devtools.html"</span>
}</code></pre>
<p>这个<code>devtools.html</code>里面一般什么都没有，就引入一个js：</p>
<pre class="html"><code class="hljs xml"><span class="hljs-meta">&lt;!DOCTYPE html&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-name">html</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-name">head</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-name">head</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-name">body</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-name">script</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"text/javascript"</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"js/devtools.js"</span>&gt;</span><span class="undefined"></span><span class="hljs-tag">&lt;/<span class="hljs-name">script</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-name">body</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-name">html</span>&gt;</span></code></pre>
<p>可以看出来，其实真正代码是<code>devtools.js</code>，html文件是“多余”的，所以这里觉得有点坑，<code>devtools_page</code>干嘛不允许直接指定JS呢？</p>
<p>再来看devtools.js的代码：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 创建自定义面板，同一个插件可以创建多个自定义面板</span>
<span class="hljs-comment">// 几个参数依次为：panel标题、图标（其实设置了也没地方显示）、要加载的页面、加载成功后的回调</span>
chrome.devtools.panels.create(<span class="hljs-string">'MyPanel'</span>, <span class="hljs-string">'img/icon.png'</span>, <span class="hljs-string">'mypanel.html'</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">panel</span>)
</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'自定义面板创建成功！'</span>); <span class="hljs-comment">// 注意这个log一般看不到</span>
});

<span class="hljs-comment">// 创建自定义侧边栏</span>
chrome.devtools.panels.elements.createSidebarPane(<span class="hljs-string">"Images"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">sidebar</span>)
</span>{
    <span class="hljs-comment">// sidebar.setPage('../sidebar.html'); // 指定加载某个页面</span>
    sidebar.setExpression(<span class="hljs-string">'document.querySelectorAll("img")'</span>, <span class="hljs-string">'All Images'</span>); <span class="hljs-comment">// 通过表达式来指定</span>
    <span class="hljs-comment">//sidebar.setObject({aaa: 111, bbb: 'Hello World!'}); // 直接设置显示某个对象</span>
});</code></pre>
<p>setPage时的效果：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101903837-2079245455.png">
<p>以下截图示例的代码：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101928103-1093454821.png">
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 检测jQuery</span>
<span class="hljs-built_in">document</span>.getElementById(<span class="hljs-string">'check_jquery'</span>).addEventListener(<span class="hljs-string">'click'</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)
</span>{
    <span class="hljs-comment">// 访问被检查的页面DOM需要使用inspectedWindow</span>
    <span class="hljs-comment">// 简单例子：检测被检查页面是否使用了jQuery</span>
    chrome.devtools.inspectedWindow.eval(<span class="hljs-string">"jQuery.fn.jquery"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">result, isException</span>)
    </span>{
        <span class="hljs-keyword">var</span> html = <span class="hljs-string">''</span>;
        <span class="hljs-keyword">if</span> (isException) html = <span class="hljs-string">'当前页面没有使用jQuery。'</span>;
        <span class="hljs-keyword">else</span> html = <span class="hljs-string">'当前页面使用了jQuery，版本为：'</span>+result;
        alert(html);
    });
});

<span class="hljs-comment">// 打开某个资源</span>
<span class="hljs-built_in">document</span>.getElementById(<span class="hljs-string">'open_resource'</span>).addEventListener(<span class="hljs-string">'click'</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)
</span>{
    chrome.devtools.inspectedWindow.eval(<span class="hljs-string">"window.location.href"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">result, isException</span>)
    </span>{
        chrome.devtools.panels.openResource(result, <span class="hljs-number">20</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)
        </span>{
            <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'资源打开成功！'</span>);
        });
    });
});

<span class="hljs-comment">// 审查元素</span>
<span class="hljs-built_in">document</span>.getElementById(<span class="hljs-string">'test_inspect'</span>).addEventListener(<span class="hljs-string">'click'</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)
</span>{
    chrome.devtools.inspectedWindow.eval(<span class="hljs-string">"inspect(document.images[0])"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">result, isException</span>)</span>{});
});

<span class="hljs-comment">// 获取所有资源</span>
<span class="hljs-built_in">document</span>.getElementById(<span class="hljs-string">'get_all_resources'</span>).addEventListener(<span class="hljs-string">'click'</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)
</span>{
    chrome.devtools.inspectedWindow.getResources(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">resources</span>)
    </span>{
        alert(<span class="hljs-built_in">JSON</span>.stringify(resources));
    });
});</code></pre>
<h3 id="调试技巧">5.5.4. 调试技巧</h3>
<p>修改了devtools页面的代码时，需要先在 <a href="chrome://extensions" class="uri" target="_blank">chrome://extensions</a> 页面按下<code>Ctrl+R</code>重新加载插件，然后关闭再打开开发者工具即可，无需刷新页面（而且只刷新页面不刷新开发者工具的话是不会生效的）。</p>
<p>由于devtools本身就是开发者工具页面，所以几乎没有方法可以直接调试它，直接用 <code>chrome-extension://extid/devtools.html"</code>的方式打开页面肯定报错，因为不支持相关特殊API，只能先自己写一些方法屏蔽这些错误，调试通了再放开。</p>
<h2 id="option选项页">5.6. option(选项页)</h2>
<p>所谓<code>options</code>页，就是插件的设置页面，有2个入口，一个是右键图标有一个“选项”菜单，还有一个在插件管理页面：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711101949603-1425351182.png">
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102004775-83441694.png">
<p>在Chrome40以前，options页面和其它普通页面没什么区别，Chrome40以后则有了一些变化。</p>
<p>我们先看老版的<a href="https://developer.chrome.com/extensions/options" target="_blank">options</a>：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-comment">// Chrome40以前的插件配置页写法</span>
    <span class="hljs-string">"options_page"</span>: <span class="hljs-string">"options.html"</span>,
}</code></pre>
<p>这个页面里面的内容就随你自己发挥了，配置之后在插件管理页就会看到一个<code>选项</code>按钮入口，点进去就是打开一个网页，没啥好讲的。</p>
<p>效果:</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102020962-228055231.png">
<p>再来看新版的<a href="https://developer.chrome.com/extensions/optionsV2" target="_blank">optionsV2</a>：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-string">"options_ui"</span>:
    {
        <span class="hljs-string">"page"</span>: <span class="hljs-string">"options.html"</span>,
        <span class="hljs-comment">// 添加一些默认的样式，推荐使用</span>
        <span class="hljs-string">"chrome_style"</span>: <span class="hljs-literal">true</span>
    },
}</code></pre>
<p><code>options.html</code>的代码我们没有任何改动，只是配置文件改了，之后效果如下：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102036665-607005369.png">
<p>看起来是不是高大上了？</p>
<p>几点注意：</p>
<ul>
<li>为了兼容，建议2种都写，如果都写了，Chrome40以后会默认读取新版的方式；</li>
<li>新版options中不能使用alert；</li>
<li>数据存储建议用chrome.storage，因为会随用户自动同步；</li>
</ul>
<h2 id="omnibox">5.7. omnibox</h2>
<p><code>omnibox</code>是向用户提供搜索建议的一种方式。先来看个<code>gif</code>图以便了解一下这东西到底是个什么鬼：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102052275-866111894.gif"></p>
<p>注册某个关键字以触发插件自己的搜索建议界面，然后可以任意发挥了。</p>
<p>首先，配置文件如下：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-comment">// 向地址栏注册一个关键字以提供搜索建议，只能设置一个关键字</span>
    <span class="hljs-string">"omnibox"</span>: { <span class="hljs-string">"keyword"</span> : <span class="hljs-string">"go"</span> },
}</code></pre>
<p>然后<code>background.js</code>中注册监听事件：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// omnibox 演示</span>
chrome.omnibox.onInputChanged.addListener(<span class="hljs-function">(<span class="hljs-params">text, suggest</span>) =&gt;</span> {
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'inputChanged: '</span> + text);
    <span class="hljs-keyword">if</span>(!text) <span class="hljs-keyword">return</span>;
    <span class="hljs-keyword">if</span>(text == <span class="hljs-string">'美女'</span>) {
        suggest([
            {<span class="hljs-attr">content</span>: <span class="hljs-string">'中国'</span> + text, <span class="hljs-attr">description</span>: <span class="hljs-string">'你要找“中国美女”吗？'</span>},
            {<span class="hljs-attr">content</span>: <span class="hljs-string">'日本'</span> + text, <span class="hljs-attr">description</span>: <span class="hljs-string">'你要找“日本美女”吗？'</span>},
            {<span class="hljs-attr">content</span>: <span class="hljs-string">'泰国'</span> + text, <span class="hljs-attr">description</span>: <span class="hljs-string">'你要找“泰国美女或人妖”吗？'</span>},
            {<span class="hljs-attr">content</span>: <span class="hljs-string">'韩国'</span> + text, <span class="hljs-attr">description</span>: <span class="hljs-string">'你要找“韩国美女”吗？'</span>}
        ]);
    }
    <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(text == <span class="hljs-string">'微博'</span>) {
        suggest([
            {<span class="hljs-attr">content</span>: <span class="hljs-string">'新浪'</span> + text, <span class="hljs-attr">description</span>: <span class="hljs-string">'新浪'</span> + text},
            {<span class="hljs-attr">content</span>: <span class="hljs-string">'腾讯'</span> + text, <span class="hljs-attr">description</span>: <span class="hljs-string">'腾讯'</span> + text},
            {<span class="hljs-attr">content</span>: <span class="hljs-string">'搜狐'</span> + text, <span class="hljs-attr">description</span>: <span class="hljs-string">'搜索'</span> + text},
        ]);
    }
    <span class="hljs-keyword">else</span> {
        suggest([
            {<span class="hljs-attr">content</span>: <span class="hljs-string">'百度搜索 '</span> + text, <span class="hljs-attr">description</span>: <span class="hljs-string">'百度搜索 '</span> + text},
            {<span class="hljs-attr">content</span>: <span class="hljs-string">'谷歌搜索 '</span> + text, <span class="hljs-attr">description</span>: <span class="hljs-string">'谷歌搜索 '</span> + text},
        ]);
    }
});

<span class="hljs-comment">// 当用户接收关键字建议时触发</span>
chrome.omnibox.onInputEntered.addListener(<span class="hljs-function">(<span class="hljs-params">text</span>) =&gt;</span> {
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'inputEntered: '</span> + text);
    <span class="hljs-keyword">if</span>(!text) <span class="hljs-keyword">return</span>;
    <span class="hljs-keyword">var</span> href = <span class="hljs-string">''</span>;
    <span class="hljs-keyword">if</span>(text.endsWith(<span class="hljs-string">'美女'</span>)) href = <span class="hljs-string">'http://image.baidu.com/search/index?tn=baiduimage&amp;ie=utf-8&amp;word='</span> + text;
    <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(text.startsWith(<span class="hljs-string">'百度搜索'</span>)) href = <span class="hljs-string">'https://www.baidu.com/s?ie=UTF-8&amp;wd='</span> + text.replace(<span class="hljs-string">'百度搜索 '</span>, <span class="hljs-string">''</span>);
    <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(text.startsWith(<span class="hljs-string">'谷歌搜索'</span>)) href = <span class="hljs-string">'https://www.google.com.tw/search?q='</span> + text.replace(<span class="hljs-string">'谷歌搜索 '</span>, <span class="hljs-string">''</span>);
    <span class="hljs-keyword">else</span> href = <span class="hljs-string">'https://www.baidu.com/s?ie=UTF-8&amp;wd='</span> + text;
    openUrlCurrentTab(href);
});
<span class="hljs-comment">// 获取当前选项卡ID</span>
<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">getCurrentTabId</span>(<span class="hljs-params">callback</span>)
</span>{
    chrome.tabs.query({<span class="hljs-attr">active</span>: <span class="hljs-literal">true</span>, <span class="hljs-attr">currentWindow</span>: <span class="hljs-literal">true</span>}, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">tabs</span>)
    </span>{
        <span class="hljs-keyword">if</span>(callback) callback(tabs.length ? tabs[<span class="hljs-number">0</span>].id: <span class="hljs-literal">null</span>);
    });
}

<span class="hljs-comment">// 当前标签打开某个链接</span>
<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">openUrlCurrentTab</span>(<span class="hljs-params">url</span>)
</span>{
    getCurrentTabId(<span class="hljs-function"><span class="hljs-params">tabId</span> =&gt;</span> {
        chrome.tabs.update(tabId, {<span class="hljs-attr">url</span>: url});
    })
}</code></pre>
<h2 id="桌面通知">5.8. 桌面通知</h2>
<p>Chrome提供了一个<code>chrome.notifications</code>API以便插件推送桌面通知，暂未找到<code>chrome.notifications</code>和HTML5自带的<code>Notification</code>的显著区别及优势。</p>
<p>在后台JS中，无论是使用<code>chrome.notifications</code>还是<code>Notification</code>都不需要申请权限（HTML5方式需要申请权限），直接使用即可。</p>
<p>最简单的通知：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102111697-515392377.png">
<p>代码：</p>
<pre class="javascript"><code class="hljs">chrome.notifications.create(<span class="hljs-literal">null</span>, {
    <span class="hljs-attr">type</span>: <span class="hljs-string">'basic'</span>,
    <span class="hljs-attr">iconUrl</span>: <span class="hljs-string">'img/icon.png'</span>,
    <span class="hljs-attr">title</span>: <span class="hljs-string">'这是标题'</span>,
    <span class="hljs-attr">message</span>: <span class="hljs-string">'您刚才点击了自定义右键菜单！'</span>
});</code></pre>
<p>通知的样式可以很丰富：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102121415-1959756640.png">
<p>这个没有深入研究，有需要的可以去看官方文档。</p>
<h1 id="种类型的js对比">5种类型的JS对比</h1>
<p>Chrome插件的JS主要可以分为这5类：<code>injected script</code>、<code>content-script</code>、<code>popup js</code>、<code>background js</code>和<code>devtools js</code>，</p>
<h2 id="权限对比">6.1. 权限对比</h2>
{% raw %}
<table>
<thead>
<tr class="header">
<th>JS种类</th>
<th>可访问的API</th>
<th>DOM访问情况</th>
<th>JS访问情况</th>
<th>直接跨域</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>injected script</td>
<td>和普通JS无任何差别，不能访问任何扩展API</td>
<td>可以访问</td>
<td>可以访问</td>
<td>不可以</td>
</tr>
<tr class="even">
<td>content script</td>
<td>只能访问 extension、runtime等部分API</td>
<td>可以访问</td>
<td>不可以</td>
<td>不可以</td>
</tr>
<tr class="odd">
<td>popup js</td>
<td>可访问绝大部分API，除了devtools系列</td>
<td>不可直接访问</td>
<td>不可以</td>
<td>可以</td>
</tr>
<tr class="even">
<td>background js</td>
<td>可访问绝大部分API，除了devtools系列</td>
<td>不可直接访问</td>
<td>不可以</td>
<td>可以</td>
</tr>
<tr class="odd">
<td>devtools js</td>
<td>只能访问 devtools、extension、runtime等部分API</td>
<td>可以</td>
<td>可以</td>
<td>不可以</td>
</tr>
</tbody>
</table>
<h2 id="调试方式对比">6.2. 调试方式对比</h2>
<table>
<thead>
<tr class="header">
<th>JS类型</th>
<th>调试方式</th>
<th>图片说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>injected script</td>
<td>直接普通的F12即可</td>
<td>懒得截图</td>
</tr>
<tr class="even">
<td>content-script</td>
<td>打开Console,如图切换</td>
<td><img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170712142454118-1741772825.png"></td>
</tr>
<tr class="odd">
<td>popup-js</td>
<td>popup页面右键审查元素</td>
<td><img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170712142508275-102456585.png"></td>
</tr>
<tr class="even">
<td>background</td>
<td>插件管理页点击背景页即可</td>
<td><img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170712142524665-745904947.png"></td>
</tr>
<tr class="odd">
<td>devtools-js</td>
<td>暂未找到有效方法</td>
<td>-</td>
</tr>
</tbody>
</table>
{% endraw %}
<h1 id="消息通信">消息通信</h1>
<p>通信主页：<a href="https://developer.chrome.com/extensions/messaging" class="uri" target="_blank">https://developer.chrome.com/extensions/messaging</a></p>
<p>前面我们介绍了Chrome插件中存在的5种JS，那么它们之间如何互相通信呢？下面先来系统概况一下，然后再分类细说。需要知道的是，popup和background其实几乎可以视为一种东西，因为它们可访问的API都一样、通信机制一样、都可以跨域。</p>
<h2 id="互相通信概览">7.1. 互相通信概览</h2>
<p>注：<code>-</code>表示不存在或者无意义，或者待验证。</p>
<table>
<thead>
<tr class="header">
<th></th>
<th>injected-script</th>
<th>content-script</th>
<th>popup-js</th>
<th>background-js</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>injected-script</td>
<td>-</td>
<td>window.postMessage</td>
<td>-</td>
<td>-</td>
</tr>
<tr class="even">
<td>content-script</td>
<td>window.postMessage</td>
<td>-</td>
<td>chrome.runtime.sendMessage chrome.runtime.connect</td>
<td>chrome.runtime.sendMessage chrome.runtime.connect</td>
</tr>
<tr class="odd">
<td>popup-js</td>
<td>-</td>
<td>chrome.tabs.sendMessage chrome.tabs.connect</td>
<td>-</td>
<td>chrome.extension. getBackgroundPage()</td>
</tr>
<tr class="even">
<td>background-js</td>
<td>-</td>
<td>chrome.tabs.sendMessage chrome.tabs.connect</td>
<td>chrome.extension.getViews</td>
<td>-</td>
</tr>
<tr class="odd">
<td>devtools-js</td>
<td>chrome.devtools. inspectedWindow.eval</td>
<td>-</td>
<td>chrome.runtime.sendMessage</td>
<td>chrome.runtime.sendMessage</td>
</tr>
</tbody>
</table>
<h2 id="通信详细介绍">7.2. 通信详细介绍</h2>
<h3 id="popup和background">7.2.1. popup和background</h3>
<p>popup可以直接调用background中的JS方法，也可以直接访问background的DOM：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// background.js</span>
<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">test</span>(<span class="hljs-params"></span>)
</span>{
    alert(<span class="hljs-string">'我是background！'</span>);
}

<span class="hljs-comment">// popup.js</span>
<span class="hljs-keyword">var</span> bg = chrome.extension.getBackgroundPage();
bg.test(); <span class="hljs-comment">// 访问bg的函数</span>
alert(bg.document.body.innerHTML); <span class="hljs-comment">// 访问bg的DOM</span></code></pre>
<blockquote>
<p>小插曲，今天碰到一个情况，发现popup无法获取background的任何方法，找了半天才发现是因为background的js报错了，而你如果不主动查看background的js的话，是看不到错误信息的，特此提醒。</p>
</blockquote>
<p>至于<code>background</code>访问<code>popup</code>如下（前提是<code>popup</code>已经打开）：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-keyword">var</span> views = chrome.extension.getViews({<span class="hljs-attr">type</span>:<span class="hljs-string">'popup'</span>});
<span class="hljs-keyword">if</span>(views.length &gt; <span class="hljs-number">0</span>) {
    <span class="hljs-built_in">console</span>.log(views[<span class="hljs-number">0</span>].location.href);
}</code></pre>
<h3 id="popup或者bg向content主动发送消息">7.2.2. popup或者bg向content主动发送消息</h3>
<p>background.js或者popup.js：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">sendMessageToContentScript</span>(<span class="hljs-params">message, callback</span>)
</span>{
    chrome.tabs.query({<span class="hljs-attr">active</span>: <span class="hljs-literal">true</span>, <span class="hljs-attr">currentWindow</span>: <span class="hljs-literal">true</span>}, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">tabs</span>)
    </span>{
        chrome.tabs.sendMessage(tabs[<span class="hljs-number">0</span>].id, message, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">response</span>)
        </span>{
            <span class="hljs-keyword">if</span>(callback) callback(response);
        });
    });
}
sendMessageToContentScript({<span class="hljs-attr">cmd</span>:<span class="hljs-string">'test'</span>, <span class="hljs-attr">value</span>:<span class="hljs-string">'你好，我是popup！'</span>}, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">response</span>)
</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'来自content的回复：'</span>+response);
});</code></pre>
<p><code>content-script.js</code>接收：</p>
<pre class="javascript"><code class="hljs">chrome.runtime.onMessage.addListener(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">request, sender, sendResponse</span>)
</span>{
    <span class="hljs-comment">// console.log(sender.tab ?"from a content script:" + sender.tab.url :"from the extension");</span>
    <span class="hljs-keyword">if</span>(request.cmd == <span class="hljs-string">'test'</span>) alert(request.value);
    sendResponse(<span class="hljs-string">'我收到了你的消息！'</span>);
});</code></pre>
<p>双方通信直接发送的都是JSON对象，不是JSON字符串，所以无需解析，很方便（当然也可以直接发送字符串）。</p>
<blockquote>
<p>网上有些老代码中用的是<code>chrome.extension.onMessage</code>，没有完全查清二者的区别(貌似是别名)，但是建议统一使用<code>chrome.runtime.onMessage</code>。</p>
</blockquote>
<h3 id="content-script主动发消息给后台">7.2.3. content-script主动发消息给后台</h3>
<p>content-script.js：</p>
<pre class="javascript"><code class="hljs">chrome.runtime.sendMessage({<span class="hljs-attr">greeting</span>: <span class="hljs-string">'你好，我是content-script呀，我主动发消息给后台！'</span>}, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">response</span>) </span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'收到来自后台的回复：'</span> + response);
});</code></pre>
<p>background.js 或者 popup.js：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 监听来自content-script的消息</span>
chrome.runtime.onMessage.addListener(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">request, sender, sendResponse</span>)
</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'收到来自content-script的消息：'</span>);
    <span class="hljs-built_in">console</span>.log(request, sender, sendResponse);
    sendResponse(<span class="hljs-string">'我是后台，我已收到你的消息：'</span> + <span class="hljs-built_in">JSON</span>.stringify(request));
});</code></pre>
<p>注意事项：</p>
<ul>
<li>content_scripts向<code>popup</code>主动发消息的前提是popup必须打开！否则需要利用background作中转；</li>
<li>如果background和popup同时监听，那么它们都可以同时收到消息，但是只有一个可以sendResponse，一个先发送了，那么另外一个再发送就无效；</li>
</ul>
<h3 id="injected-script和content-script">7.2.4. injected script和content-script</h3>
<p><code>content-script</code>和页面内的脚本（<code>injected-script</code>自然也属于页面内的脚本）之间唯一共享的东西就是页面的DOM元素，有2种方法可以实现二者通讯：</p>
<ol>
<li>可以通过<code>window.postMessage</code>和<code>window.addEventListener</code>来实现二者消息通讯；</li>
<li>通过自定义DOM事件来实现；</li>
</ol>
<p>第一种方法（推荐）：</p>
<p><code>injected-script</code>中：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-built_in">window</span>.postMessage({<span class="hljs-string">"test"</span>: <span class="hljs-string">'你好！'</span>}, <span class="hljs-string">'*'</span>);</code></pre>
<p>content script中：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-built_in">window</span>.addEventListener(<span class="hljs-string">"message"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">e</span>)
</span>{
    <span class="hljs-built_in">console</span>.log(e.data);
}, <span class="hljs-literal">false</span>);</code></pre>
<p>第二种方法：</p>
<p><code>injected-script</code>中：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-keyword">var</span> customEvent = <span class="hljs-built_in">document</span>.createEvent(<span class="hljs-string">'Event'</span>);
customEvent.initEvent(<span class="hljs-string">'myCustomEvent'</span>, <span class="hljs-literal">true</span>, <span class="hljs-literal">true</span>);
<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">fireCustomEvent</span>(<span class="hljs-params">data</span>) </span>{
    hiddenDiv = <span class="hljs-built_in">document</span>.getElementById(<span class="hljs-string">'myCustomEventDiv'</span>);
    hiddenDiv.innerText = data
    hiddenDiv.dispatchEvent(customEvent);
}
fireCustomEvent(<span class="hljs-string">'你好，我是普通JS！'</span>);</code></pre>
<p><code>content-script.js</code>中：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-keyword">var</span> hiddenDiv = <span class="hljs-built_in">document</span>.getElementById(<span class="hljs-string">'myCustomEventDiv'</span>);
<span class="hljs-keyword">if</span>(!hiddenDiv) {
    hiddenDiv = <span class="hljs-built_in">document</span>.createElement(<span class="hljs-string">'div'</span>);
    hiddenDiv.style.display = <span class="hljs-string">'none'</span>;
    <span class="hljs-built_in">document</span>.body.appendChild(hiddenDiv);
}
hiddenDiv.addEventListener(<span class="hljs-string">'myCustomEvent'</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>) </span>{
    <span class="hljs-keyword">var</span> eventData = <span class="hljs-built_in">document</span>.getElementById(<span class="hljs-string">'myCustomEventDiv'</span>).innerText;
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'收到自定义事件消息：'</span> + eventData);
});</code></pre>
<h2 id="长连接和短连接">7.3. 长连接和短连接</h2>
<p>其实上面已经涉及到了，这里再单独说明一下。Chrome插件中有2种通信方式，一个是短连接（<code>chrome.tabs.sendMessage</code>和<code>chrome.runtime.sendMessage</code>），一个是长连接（<code>chrome.tabs.connect</code>和<code>chrome.runtime.connect</code>）。</p>
<p>短连接的话就是挤牙膏一样，我发送一下，你收到了再回复一下，如果对方不回复，你只能重新发，而长连接类似<code>WebSocket</code>会一直建立连接，双方可以随时互发消息。</p>
<p>短连接上面已经有代码示例了，这里只讲一下长连接。</p>
<p>popup.js：</p>
<pre class="javascript"><code class="hljs">getCurrentTabId(<span class="hljs-function">(<span class="hljs-params">tabId</span>) =&gt;</span> {
    <span class="hljs-keyword">var</span> port = chrome.tabs.connect(tabId, {<span class="hljs-attr">name</span>: <span class="hljs-string">'test-connect'</span>});
    port.postMessage({<span class="hljs-attr">question</span>: <span class="hljs-string">'你是谁啊？'</span>});
    port.onMessage.addListener(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">msg</span>) </span>{
        alert(<span class="hljs-string">'收到消息：'</span>+msg.answer);
        <span class="hljs-keyword">if</span>(msg.answer &amp;&amp; msg.answer.startsWith(<span class="hljs-string">'我是'</span>))
        {
            port.postMessage({<span class="hljs-attr">question</span>: <span class="hljs-string">'哦，原来是你啊！'</span>});
        }
    });
});</code></pre>
<p>content-script.js：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 监听长连接</span>
chrome.runtime.onConnect.addListener(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">port</span>) </span>{
    <span class="hljs-built_in">console</span>.log(port);
    <span class="hljs-keyword">if</span>(port.name == <span class="hljs-string">'test-connect'</span>) {
        port.onMessage.addListener(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">msg</span>) </span>{
            <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'收到长连接消息：'</span>, msg);
            <span class="hljs-keyword">if</span>(msg.question == <span class="hljs-string">'你是谁啊？'</span>) port.postMessage({<span class="hljs-attr">answer</span>: <span class="hljs-string">'我是你爸！'</span>});
        });
    }
});</code></pre>
<h1 id="其它补充">其它补充</h1>
<h2 id="动态注入或执行js">8.1. 动态注入或执行JS</h2>
<p>虽然在<code>background</code>和<code>popup</code>中无法直接访问页面DOM，但是可以通过<code>chrome.tabs.executeScript</code>来执行脚本，从而实现访问web页面的DOM（注意，这种方式也不能直接访问页面JS）。</p>
<p>示例<code>manifest.json</code>配置：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-string">"name"</span>: <span class="hljs-string">"动态JS注入演示"</span>,
    ...
    <span class="hljs-string">"permissions"</span>: [
        <span class="hljs-string">"tabs"</span>, <span class="hljs-string">"http://*/*"</span>, <span class="hljs-string">"https://*/*"</span>
    ],
    ...
}</code></pre>
<p>JS：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 动态执行JS代码</span>
chrome.tabs.executeScript(tabId, {<span class="hljs-attr">code</span>: <span class="hljs-string">'document.body.style.backgroundColor="red"'</span>});
<span class="hljs-comment">// 动态执行JS文件</span>
chrome.tabs.executeScript(tabId, {<span class="hljs-attr">file</span>: <span class="hljs-string">'some-script.js'</span>});</code></pre>
<h2 id="动态注入css">8.2. 动态注入CSS</h2>
<p>示例<code>manifest.json</code>配置：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-string">"name"</span>: <span class="hljs-string">"动态CSS注入演示"</span>,
    ...
    <span class="hljs-string">"permissions"</span>: [
        <span class="hljs-string">"tabs"</span>, <span class="hljs-string">"http://*/*"</span>, <span class="hljs-string">"https://*/*"</span>
    ],
    ...
}</code></pre>
<p>JS代码：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 动态执行CSS代码，TODO，这里有待验证</span>
chrome.tabs.insertCSS(tabId, {<span class="hljs-attr">code</span>: <span class="hljs-string">'xxx'</span>});
<span class="hljs-comment">// 动态执行CSS文件</span>
chrome.tabs.insertCSS(tabId, {<span class="hljs-attr">file</span>: <span class="hljs-string">'some-style.css'</span>});</code></pre>
<h2 id="获取当前窗口id">8.3. 获取当前窗口ID</h2>
<pre class="javascript"><code class="hljs">chrome.windows.getCurrent(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">currentWindow</span>)
</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'当前窗口ID：'</span> + currentWindow.id);
});</code></pre>
<h2 id="获取当前标签页id">8.4. 获取当前标签页ID</h2>
<p>一般有2种方法：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 获取当前选项卡ID</span>
<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">getCurrentTabId</span>(<span class="hljs-params">callback</span>)
</span>{
    chrome.tabs.query({<span class="hljs-attr">active</span>: <span class="hljs-literal">true</span>, <span class="hljs-attr">currentWindow</span>: <span class="hljs-literal">true</span>}, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">tabs</span>)
    </span>{
        <span class="hljs-keyword">if</span>(callback) callback(tabs.length ? tabs[<span class="hljs-number">0</span>].id: <span class="hljs-literal">null</span>);
    });
}</code></pre>
<p>获取当前选项卡id的另一种方法，大部分时候都类似，只有少部分时候会不一样（例如当窗口最小化时）</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 获取当前选项卡ID</span>
<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">getCurrentTabId2</span>(<span class="hljs-params"></span>)
</span>{
    chrome.windows.getCurrent(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">currentWindow</span>)
    </span>{
        chrome.tabs.query({<span class="hljs-attr">active</span>: <span class="hljs-literal">true</span>, <span class="hljs-attr">windowId</span>: currentWindow.id}, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">tabs</span>)
        </span>{
            <span class="hljs-keyword">if</span>(callback) callback(tabs.length ? tabs[<span class="hljs-number">0</span>].id: <span class="hljs-literal">null</span>);
        });
    });
}</code></pre>
<h2 id="本地存储">8.5. 本地存储</h2>
<p>本地存储建议用<code>chrome.storage</code>而不是普通的<code>localStorage</code>，区别有好几点，个人认为最重要的2点区别是：</p>
<ul>
<li><code>chrome.storage</code>是针对插件全局的，即使你在<code>background</code>中保存的数据，在<code>content-script</code>也能获取到；</li>
<li><code>chrome.storage.sync</code>可以跟随当前登录用户自动同步，这台电脑修改的设置会自动同步到其它电脑，很方便，如果没有登录或者未联网则先保存到本地，等登录了再同步至网络；</li>
</ul>
<p>需要声明<code>storage</code>权限，有<code>chrome.storage.sync</code>和<code>chrome.storage.local</code>2种方式可供选择，使用示例如下：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">// 读取数据，第一个参数是指定要读取的key以及设置默认值</span>
chrome.storage.sync.get({<span class="hljs-attr">color</span>: <span class="hljs-string">'red'</span>, <span class="hljs-attr">age</span>: <span class="hljs-number">18</span>}, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">items</span>) </span>{
    <span class="hljs-built_in">console</span>.log(items.color, items.age);
});
<span class="hljs-comment">// 保存数据</span>
chrome.storage.sync.set({<span class="hljs-attr">color</span>: <span class="hljs-string">'blue'</span>}, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>) </span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">'保存成功！'</span>);
});</code></pre>
<h2 id="webrequest">8.6. webRequest</h2>
<p>通过webRequest系列API可以对HTTP请求进行任性地修改、定制，这里通过<code>beforeRequest</code>来简单演示一下它的冰山一角：</p>
<pre class="javascript"><code class="hljs"><span class="hljs-comment">//manifest.json</span>
{
    <span class="hljs-comment">// 权限申请</span>
    <span class="hljs-string">"permissions"</span>:
    [
        <span class="hljs-string">"webRequest"</span>, <span class="hljs-comment">// web请求</span>
        <span class="hljs-string">"webRequestBlocking"</span>, <span class="hljs-comment">// 阻塞式web请求</span>
        <span class="hljs-string">"storage"</span>, <span class="hljs-comment">// 插件本地存储</span>
        <span class="hljs-string">"http://*/*"</span>, <span class="hljs-comment">// 可以通过executeScript或者insertCSS访问的网站</span>
        <span class="hljs-string">"https://*/*"</span> <span class="hljs-comment">// 可以通过executeScript或者insertCSS访问的网站</span>
    ],
}


<span class="hljs-comment">// background.js</span>
<span class="hljs-comment">// 是否显示图片</span>
<span class="hljs-keyword">var</span> showImage;
chrome.storage.sync.get({<span class="hljs-attr">showImage</span>: <span class="hljs-literal">true</span>}, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">items</span>) </span>{
    showImage = items.showImage;
});
<span class="hljs-comment">// web请求监听，最后一个参数表示阻塞式，需单独声明权限：webRequestBlocking</span>
chrome.webRequest.onBeforeRequest.addListener(<span class="hljs-function"><span class="hljs-params">details</span> =&gt;</span> {
    <span class="hljs-comment">// cancel 表示取消本次请求</span>
    <span class="hljs-keyword">if</span>(!showImage &amp;&amp; details.type == <span class="hljs-string">'image'</span>) <span class="hljs-keyword">return</span> {<span class="hljs-attr">cancel</span>: <span class="hljs-literal">true</span>};
    <span class="hljs-comment">// 简单的音视频检测</span>
    <span class="hljs-comment">// 大部分网站视频的type并不是media，且视频做了防下载处理，所以这里仅仅是为了演示效果，无实际意义</span>
    <span class="hljs-keyword">if</span>(details.type == <span class="hljs-string">'media'</span>) {
        chrome.notifications.create(<span class="hljs-literal">null</span>, {
            <span class="hljs-attr">type</span>: <span class="hljs-string">'basic'</span>,
            <span class="hljs-attr">iconUrl</span>: <span class="hljs-string">'img/icon.png'</span>,
            <span class="hljs-attr">title</span>: <span class="hljs-string">'检测到音视频'</span>,
            <span class="hljs-attr">message</span>: <span class="hljs-string">'音视频地址：'</span> + details.url,
        });
    }
}, {<span class="hljs-attr">urls</span>: [<span class="hljs-string">"&lt;all_urls&gt;"</span>]}, [<span class="hljs-string">"blocking"</span>]);</code></pre>
<h2 id="国际化">8.7. 国际化</h2>
<p>插件根目录新建一个名为<code>_locales</code>的文件夹，再在下面新建一些语言的文件夹，如<code>en</code>、<code>zh_CN</code>、<code>zh_TW</code>，然后再在每个文件夹放入一个<code>messages.json</code>，同时必须在清单文件中设置<code>default_locale</code>。</p>
<p><code>_locales\en\messages.json</code>内容：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-string">"pluginDesc"</span>: {<span class="hljs-string">"message"</span>: <span class="hljs-string">"A simple chrome extension demo"</span>},
    <span class="hljs-string">"helloWorld"</span>: {<span class="hljs-string">"message"</span>: <span class="hljs-string">"Hello World!"</span>}
}</code></pre>
<p><code>_locales\zh_CN\messages.json</code>内容：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-string">"pluginDesc"</span>: {<span class="hljs-string">"message"</span>: <span class="hljs-string">"一个简单的Chrome插件demo"</span>},
    <span class="hljs-string">"helloWorld"</span>: {<span class="hljs-string">"message"</span>: <span class="hljs-string">"你好啊，世界！"</span>}
}</code></pre>
<p>在<code>manifest.json</code>和<code>CSS</code>文件中通过<code>__MSG_messagename__</code>引入，如：</p>
<pre class="javascript"><code class="hljs">{
    <span class="hljs-string">"description"</span>: <span class="hljs-string">"__MSG_pluginDesc__"</span>,
    <span class="hljs-comment">// 默认语言</span>
    <span class="hljs-string">"default_locale"</span>: <span class="hljs-string">"zh_CN"</span>,
}</code></pre>
<p>JS中则直接<code>chrome.i18n.getMessage("helloWorld")</code>。</p>
<p>测试时，通过给chrome建立一个不同的快捷方式<code>chrome.exe --lang=en</code>来切换语言，如：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102158259-417770023.png">
<p>英文效果：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102210868-1502746521.png">
<p>中文效果：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102221478-1089926751.png">
<h2 id="api总结">8.8. API总结</h2>
<p>比较常用用的一些API系列：</p>
<ul>
<li>chrome.tabs</li>
<li>chrome.runtime</li>
<li>chrome.webRequest</li>
<li>chrome.window</li>
<li>chrome.storage</li>
<li>chrome.contextMenus</li>
<li>chrome.devtools</li>
<li>chrome.extension</li>
</ul>
<h1 id="经验总结">经验总结</h1>
<h2 id="查看已安装插件路径">9.1. 查看已安装插件路径</h2>
<p>已安装的插件源码路径：<code>C:\Users\用户名\AppData\Local\Google\Chrome\User Data\Default\Extensions</code>，每一个插件被放在以插件ID为名的文件夹里面，想要学习某个插件的某个功能是如何实现的，看人家的源码是最好的方法了：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102256712-51940037.png">
<p>如何查看某个插件的ID？进入 <a href="chrome://extensions" class="uri" target="_blank">chrome://extensions</a> ，然后勾线开发者模式即可看到了。</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102308431-360409003.png">
<h2 id="特别注意background的报错">9.2. 特别注意background的报错</h2>
<p>很多时候你发现你的代码会莫名其妙的失效，找来找去又找不到原因，这时打开background的控制台才发现原来某个地方写错了导致代码没生效，正式由于background报错的隐蔽性(需要主动打开对应的控制台才能看到错误)，所以特别注意这点。</p>
<h2 id="如何让popup页面不关闭">9.3. 如何让popup页面不关闭</h2>
<p>在对popup页面审查元素的时候popup会被强制打开无法关闭，只有控制台关闭了才可以关闭popup，原因很简单：如果popup关闭了控制台就没用了。这种方法在某些情况下很实用！</p>
<h2 id="不支持内联javascript的执行">9.4. 不支持内联JavaScript的执行</h2>
<p>也就是不支持将js直接写在html中，比如：</p>
<pre class="html"><code class="hljs xml"><span class="hljs-tag">&lt;<span class="hljs-name">input</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"btn"</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"button"</span> <span class="hljs-attr">value</span>=<span class="hljs-string">"收藏"</span> <span class="hljs-attr">onclick</span>=<span class="hljs-string">"test()"</span>/&gt;</span></code></pre>
<p>报错如下：</p>
<pre><code class="hljs sql">Refused to <span class="hljs-keyword">execute</span> inline <span class="hljs-keyword">event</span> <span class="hljs-keyword">handler</span> because it violates the <span class="hljs-keyword">following</span> <span class="hljs-keyword">Content</span> <span class="hljs-keyword">Security</span> <span class="hljs-keyword">Policy</span> directive: <span class="hljs-string">"script-src 'self' blob: filesystem: chrome-extension-resource:"</span>. Either the <span class="hljs-string">'unsafe-inline'</span> keyword, a <span class="hljs-keyword">hash</span> (<span class="hljs-string">'sha256-...'</span>), <span class="hljs-keyword">or</span> a nonce (<span class="hljs-string">'nonce-...'</span>) <span class="hljs-keyword">is</span> <span class="hljs-keyword">required</span> <span class="hljs-keyword">to</span> <span class="hljs-keyword">enable</span> inline execution.</code></pre>
<p>解决方法就是用JS绑定事件：</p>
<pre class="javascript"><code class="hljs">$(<span class="hljs-string">'#btn'</span>).on(<span class="hljs-string">'click'</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{alert(<span class="hljs-string">'测试'</span>)});</code></pre>
<p>另外，对于A标签，这样写<code>href="javascript:;"</code>然后用JS绑定事件虽然控制台会报错，但是不受影响，当然强迫症患者受不了的话只能写成<code>href="#"</code>了。</p>
<p>如果这样写：</p>
<pre><code class="hljs xml"><span class="hljs-tag">&lt;<span class="hljs-name">a</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"javascript:;"</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"get_secret"</span>&gt;</span>请求secret<span class="hljs-tag">&lt;/<span class="hljs-name">a</span>&gt;</span></code></pre>
<p>报错如下：</p>
<pre><code class="hljs sql">Refused to <span class="hljs-keyword">execute</span> JavaScript <span class="hljs-keyword">URL</span> because it violates the <span class="hljs-keyword">following</span> <span class="hljs-keyword">Content</span> <span class="hljs-keyword">Security</span> <span class="hljs-keyword">Policy</span> directive: <span class="hljs-string">"script-src 'self' blob: filesystem: chrome-extension-resource:"</span>. Either the <span class="hljs-string">'unsafe-inline'</span> keyword, a <span class="hljs-keyword">hash</span> (<span class="hljs-string">'sha256-...'</span>), <span class="hljs-keyword">or</span> a nonce (<span class="hljs-string">'nonce-...'</span>) <span class="hljs-keyword">is</span> <span class="hljs-keyword">required</span> <span class="hljs-keyword">to</span> <span class="hljs-keyword">enable</span> inline execution.</code></pre>
<h2 id="注入css的时候必须小心">9.5. 注入CSS的时候必须小心</h2>
<p>由于通过<code>content_scripts</code>注入的CSS优先级非常高，几乎仅次于浏览器默认样式，稍不注意可能就会影响一些网站的展示效果，所以尽量不要写一些影响全局的样式。</p>
<p>之所以强调这个，是因为这个带来的问题非常隐蔽，不太容易找到，可能你正在写某个网页，昨天样式还是好好的，怎么今天就突然不行了？然后你辛辛苦苦找来找去，找了半天才发现竟然是因为插件里面的一个样式影响的！</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102324665-787100296.png">
<h1 id="打包与发布">打包与发布</h1>
<p>打包的话直接在插件管理页有一个打包按钮：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102349728-1968586800.png">
<p>然后会生成一个<code>.crx</code>文件，要发布到Google应用商店的话需要先登录你的Google账号，然后花5个$注册为开发者，本人太穷，就懒得亲自验证了，有发布需求的自己去整吧。</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102401665-879882813.png">
<h1 id="参考">参考</h1>
<h2 id="官方资料">11.1. 官方资料</h2>
<p>推荐查看官方文档，虽然是英文，但是全且新，国内的中文资料都比较旧（注意以下全部需要FQ）：</p>
<ul>
<li><a href="https://developer.chrome.com/extensions" target="_blank">Chrome插件官方文档主页</a></li>
<li><a href="https://developer.chrome.com/extensions/samples" target="_blank">Chrome插件官方示例</a></li>
<li><a href="https://developer.chrome.com/extensions/manifest" target="_blank">manifest清单文件</a></li>
<li><a href="https://developer.chrome.com/extensions/permissions" target="_blank">permissions权限</a></li>
<li><a href="https://developer.chrome.com/extensions/api_index" target="_blank">chrome.xxx.api文档</a></li>
<li><a href="https://developer.chrome.com/extensions/match_patterns" target="_blank">模糊匹配规则语法详解</a></li>
</ul>
<h2 id="第三方资料">11.2. 第三方资料</h2>
<p>部分中文资料，不是特别推荐：</p>
<ul>
<li><a href="http://open.se.360.cn/open/extension_dev/overview.html" target="_blank">360安全浏览器开发文档</a></li>
<li><a href="http://open.chrome.360.cn/extension_dev/overview.html" target="_blank">360极速浏览器Chrome扩展开发文档</a></li>
<li><a href="http://www.cnblogs.com/champagne/p/" target="_blank">Chrome扩展开发极客系列博客</a></li>
</ul>
<h1 id="附图">附图</h1>
<p>附图：Chrome高清png格式logo：</p>
<img src="https://images2015.cnblogs.com/blog/352797/201707/352797-20170711102416728-1289897384.png">
</div><div id="MySignature" style="display: block;">个人网站：<a href="https://haoji.me/" target="_blank">https://haoji.me</a><br>
github：<a href="https://github.com/sxei" target="_blank">https://github.com/sxei</a><br>
博客园：<a href="http://www.cnblogs.com/liuxianan" target="_blank">http://www.cnblogs.com/liuxianan</a><br>
copyright ©2012-2018 小茗同学
<p style="color: #FF8301; margin-top: 20px">【转载文章务必保留出处和署名，谢谢！】</p></div>
文章来源：
			<a id="cb_post_title_url" class="postTitle2" href="https://www.cnblogs.com/liuxianan/p/chrome-plugin-develop.html">【干货】Chrome插件(扩展)开发全攻略</a>
		
		