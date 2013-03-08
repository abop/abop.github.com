---
layout: post
description: ""
category: Java
tags: [webx, maven, velocity]
published: true
---

Maven, Webx, Velocity 学习总结
==========

* [综述](#overview)
* [Maven](#maven)
  * [Maven基础](#maven_basics)
  * [POM基础](#pom)
  * [依赖管理](#dependency)
* [Webx](#webx)
  * [一分钟Webx框架开发](#one_m_webx)
    * [处理页面的基本流程（Turbine）](#turbine)
    * [Webx各层我们要做的工作的工作](#layer)
  * [Webx框架初探](#webx_platform)
    * [Webx的基本概念](#webx_basics)
    * [Webx项目目录结构](#webx_dir_structure)
    * [Webx应用启动流程](#webx_start_process)
    * [Webx响应和处理请求的流程](#webx_request_handdling)
* [Velocity](#velocity)
* [Ibatis](#ibatis)
  * [Ibatis的作用](#ibatis_use)
  * [Ibatis的配置](#ibatis_config)
* [参考资料](#ref)

<h2 id="overview">综述</h2>

这两周的时间，通过看指南，看代码，跑实例，对淘宝网web应用开发框架有了一个整体的初步认识，主要包括四个方面：

1. 项目管理工具Maven

2. MVC框架Webx。

3. Webx中的View层实现——Velocity

4. Webx中的Model持久层实现——Ibatis

<h2 id="maven">Maven</h2>

<h3 id="maven_basics">Maven基础</h3>

1. 坐标是指项目的唯一标识，以GAV（groupId，artifactId和version）区分， groupId包含公司和内部组（或产品线）的信息，如com.taobao.mercury，表示taobao.com下的mercury项目组；ArtifactId则表示项目的名称，如mercury-web，Version则表示某一Artifact的版本，如1.0-SNAPSHOT

2. 依赖是指项目所依赖的相关jar包

3. 仓库是指存放项目的产出（Artifact）的仓库，如http://mvnrepo.taobao.ali.com/mvn/repository

4. 插件是Maven的设计机制，Maven包含一个核心功能组件，而扩展功能都是通过插件完成，即使我们最常见的编译功能也是通过compiler插件完成的。

<h3 id="pom">POM基础</h3>

一个pom.xml文件包含以下基本信息：modelVersion, groupId, artifactId, version, packaging, name, description, url, licenses, scm, connection, developers

<h3 id="dependency">依赖管理</h3>

1. 依赖定义具体到version

2. 依赖可以传递

3. &lt;scope>标签定义依赖项的生效范围

4. 依赖的版本定义在父pom（项目pom）中，子pom（模块pom）设置&lt;parent>，继承父pom

5. 设置一个虚拟的总项目，多个实际项目作为总项目的模块

<h2 id="webx">Webx</h2>

<h3 id="one_m_webx">一分钟Webx框架开发</h3>

<h4 id="turbine">理页面的基本流程（Turbine）</h4>

Webx是采用MVC三层架构。请求的上下文信息（RequestContext、ServletContext、HttpServletRequest、HttpServletResponse）被封装为RunData对象。在Web层和Biz层之间采用command模式实现对请求的处理。由MappingRuleService将请求分发到同名的业务处理类进行处理，处理结果被封装为Result对象。然后按照业务逻辑将所需信息放到TemplateContext中，由velocity取得并对VM模板进行渲染。最后产出的页面输出给用户。

有的请求并不要求返回页面，只需处理业务逻辑。

整个处理流程的控制由Pipeline（水管）中配置各种Valve（阀门）实现，简易的流程图如下：



图表 3‑1

<h4 id="layer">Webx各层我们要做的工作的工作</h4>

* V层(Web)：采用Velocity书写vm模版，书写一个同名的Java类来接收Command，并处理业务逻辑（void execute(RunData,TemplateContext)），并根据处理得到的结果(Result对象)，准备好vm模版中所需要的数据（TemplateContext.put(String key, Object value)）。

* C层(Biz)：书写AO对象处理Command，请求的上下文信息被封装在RunData对象中。AO中只包含简单的逻辑处理，真正的业务处理通常书写在Manager中。

* M层(ibatis)：书写DAO对象负责和数据交互，持久层使用的是Ibatis。需要我们来建立数据物理模型并书写数据库操作的SQL语句。

<h3 id="webx_platform">Webx框架初探</h3>

<h4 id="webx_basics">Webx的基本概念</h4>

1. 页面驱动：以页面（view）为主导，先写页面，再写和页面配套的程序模块，通过规则，查找页面所对应的程序模块
2. Pull Tools：有一些定义在webx.xml中，另一些默认定义在框架中。这些tools可以再vm模板中被直接引用。Global作用域的tool在系统初始化时创建，Request作用域的tool在每次HTTP请求时创建。
3. Modules：turbine中的基本编程模块。Screen用来处理页面显示逻辑；Control和screen类似，但可以被别的screen或layout引用；Action处理用户提交表单。所有module都实现Module接口：public interface Module {void execute(RunData rundata) throws WebxException;}

<h4 id="webx_dir_structure">Webx项目目录结构</h4>

    mercury  
    ├─assets
    ├─biz
    │ ├─src  
    │ │ ├─main.java.com.taobao
    │ │ │ │ └─mercury
    │ │ │ │ ├─amend
    │ │ │ │ ├─ao
    │ │ │ │ │ ├─correct
    │ │ │ │ │ │ └─impl
    │ │ │ │ │ └─impl
    │ │ │ │ ├─aop
    │ │ │ │ ├─biz
    │ │ │ │ │ ├─domain
    │ │ │ │ │ │ ├─mysqlmap
    │ │ │ │ │ │ ├─oracle
    │ │ │ │ │ │ ├─rowkey
    │ │ │ │ │ │ └─sajiao
    │ │ │ │ │ └─util
    │ │ │ │ ├─cache
    │ │ │ │ ├─dal
    │ │ │ │ │ └─sajiao
    │ │ │ │ ├─mananger
    │ │ │ │ │ └─sajiao
    │ │ │ │ ├─notify
    │ │ │ │ ├─ob
    │ │ │ │ │ ├─ao
    │ │ │ │ │ ├─dal
    │ │ │ │ │ └─mananger
    │ │ │ │ ├─query
    │ │ │ │ └─timetask
    │ │ │ └─resources
    │ │ └─test
    │ └─target
    ├─deploy
    ├─protocal
    │ ├─.settings
    │ ├─src
    │ │ ├─main.java.com.taobao
    │ │ │ ├─common
    │ │ │ │─mercury
    │ │ │ │ ├─domain
    │ │ │ │ └─util
    │ │ │ └─partition
    │ │ └─test
    │ └─target
    └─web
       ├─.settings
       ├─src
       │ ├─main
       │ │ ├─assembly
       │ │ ├─filter
       │ │ ├─java.com.taobao.mercury.web
       │ │ │ ├─common
       │ │ │ │ ├─service
       │ │ │ │ ├─session
       │ │ │ │ ├─util
       │ │ │ │ └─valve
       │ │ │ └─module
       │ │ │ ├─action
       │ │ │ ├─control
       │ │ │ └─screen
       │ │ ├─resources
       │ │ │ ├─biz
       │ │ │ │ ├─bean
       │ │ │ │ └─persistence
       │ │ │ └─top
       │ │ │ └─config
       │ │ └─webapp
       │ │ ├─common
       │ │ │ └─templates
       │ │ │ └─screen
       │ │ ├─default
       │ │ │ └─templates
       │ │ │ ├─control
       │ │ │ ├─layout
       │ │ │ └─screen
       │ │ ├─META-INF
       │ │ └─WEB-INF
       │ │ ├─common
       │ │ └─default
       │ └─test.java.com.taobao.mercury
       └─target

<h4 id="webx_start_process">Webx应用启动流程<h4/>

Webapp\WEB-INF\web.xml是SpringExt的配置文件，下面是mercury-web中的样本：

{% highlight xml %}
<?xml version="1.0" encoding="GB2312"?>

<web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"

xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">

<context-param>

<param-name>loggingRoot</param-name>

<param-value>${mercury.loggingRoot}</param-value>

</context-param>

......

<listener><listener-class>com.alibaba.citrus.logconfig.LogConfiguratorListener</listener-class>

</listener>

<!-- ???/WEB-INF/webx.xml, /WEB-INF/webx-*.xml -->

<listener>

<listener-class>com.alibaba.citrus.webx.context.WebxContextLoaderListener</listener-class>

</listener>

......

<filter>

<filter-name>sessionFilter</filter-name>

<filter-class>com.taobao.session.TaobaoSessionFilter</filter-class>

<init-param>

<param-name>tbsessionConfigGroup</param-name>

<param-value>${mercury.session.filter.group.name}</param-value>

</init-param>

<init-param>

<param-name>isloginSessionCheck</param-name>

<param-value>true</param-value>

</init-param>

</filter>

<filter-mapping>

<filter-name>sessionFilter</filter-name>

<url-pattern>*.htm</url-pattern>

</filter-mapping>

......

<filter>

<filter-name>timer</filter-name>

<filter-class>com.alibaba.webx.filter.timer.TimerFilter</filter-class>

<init-param>

<param-name>threshold</param-name>

<param-value>${mercury.timer.threshold}</param-value>

</init-param>

</filter>

<filter-mapping>

<filter-name>timer</filter-name>

<url-pattern>/*</url-pattern>

</filter-mapping>

<filter>

<filter-name>webx</filter-name>

<filter-class>com.alibaba.citrus.webx.servlet.WebxFrameworkFilter</filter-class>

</filter>

<filter>

<filter-name>mdc</filter-name>

<filter-class>com.alibaba.citrus.webx.servlet.SetLoggingContextFilter</filter-class>

</filter>

<filter-mapping>

<filter-name>mdc</filter-name>

<url-pattern>/*</url-pattern>

</filter-mapping>

<filter-mapping>

<filter-name>webx</filter-name>

<url-pattern>/*</url-pattern>

</filter-mapping>

<welcome-file-list>

<welcome-file>index.html</welcome-file>

<welcome-file>index.jsp</welcome-file>

</welcome-file-list>

<!-- ====================================================== -->

<!-- error pages -->

<!-- ====================================================== -->

<error-page>

<error-code>400</error-code>

<location>/error.html</location>

</error-page>

......

</web-app>
{% endhighlight %}

一个Webx应用的启动流程如下：

1. 初始化context-param

2. 执行各个listener，其中WebxContextLoaderListener初始化被Webx包装过的XmlWebApplicationContext（spring容器）及各个子容器，各种webx.xml里定义beans（包括&lt;service>标签定义的服务）都被装载。

3. 加载各个filter：其中TimerFilter（timer）用来记录serverlet执行时间，WebxFrameworkFilter（webx）用来处理web请求，SetLoggingContextFilter（mdc）用来通过SLF4J MDC来记录用户和请求的信息。三者顺序为timer->webx->mdc。

 [web.xml中各种元素的含义](http://ajava.org/readbook/J2EE/servletjsphxbc2/17212.html)

<h4 id="webx_request_handdling">Webx响应和处理请求的流程</h4>

（VIA: [Webx框架指南](http://openwebx.org/docs/webxframework.html#d0e1895)）

当一个HTTP请求到达时，首先由WebxFrameworkFilter接手这个请求（参考Webx应用启动流程），下图是WebxFrameworkFilter处理一个WEB请求的过程：



如图所示，WebxFrameworkFilter接到请求以后，就会调用WebxRootController。从这里开始，进入Spring的世界。此后所有的对象：WebxRootController、WebxController、RequestContext、Pipeline等，全部是通过SpringExt配置在Spring Context中的。

WebxRootController对象存在于root context中，它被所有子应用所共享。它会创建RequestContext实例，从而增强request、response、session的功能。接下来，WebxController对象会被调用。

WebxController对象是由每个子应用独享的，子应用app1和app2可以有不同的WebxController实现。默认的实现，会调用pipeline。

Pipeline也是由各子应用自己来配置的。假如pipeline碰到无法处理的请求，如静态页面、图片等，pipeline应当执行&lt;exit/> valve强制退出。然后WebxRootController就会“放弃控制”，这意味着request将被返还给/WEB-INF/web.xml中定义的servlet、filter或者返还给servlet engine本身来处理。

webapp\common\pipeline.xml配置了mercury-web的PipeLine：

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8" ?>

<beans:beans xmlns:services="http://www.alibaba.com/schema/services"

......

xsi:schemaLocation="http://www.alibaba.com/schema/services

...... "

>

<services:pipeline

xmlns="http://www.alibaba.com/schema/services/pipeline/valves">

<prepareForTurbine />

<prepareForWebx2/>

<pl-valves:try-catch-finally>

<pl-valves:try>

<webx2-valve class="com.alibaba.turbine.pipeline.SetLoggingContextValve" />

<webx2-valve class="com.taobao.mercury.web.common.valve.DefaultAnalyzeURLValve" />

<webx2-valve

class="com.taobao.mercury.web.common.valve.SecurityCheckValve" />

<webx2-valve class="com.taobao.mercury.web.common.valve.SessionValve" />

<webx2-valve class="com.taobao.common.pagecache.webx.PageCacheValve"

p:action="start" />

<loop>

<choose>

<when>

<pl-conditions:target-extension-condition

extension="vm, jsp" />

<performAction />

<performTemplateScreen />

<renderTemplate />

</when>

<when>

<pl-conditions:target-extension-condition

extension="do" />

<performAction />

<performScreen />

</when>

<otherwise>

<exit />

</otherwise>

</choose>

<breakUnlessTargetRedirected />

</loop>

</pl-valves:try>

<pl-valves:finally>

<webx2-valve class="com.taobao.common.pagecache.webx.PageCacheValve"

p:action="end" />

<webx2-valve class="com.taobao.mercury.web.common.valve.SessionValve"

p:action="clean" />

<webx2-valve class="com.alibaba.turbine.pipeline.SetLoggingContextValve"

p:action="cleanup" />

</pl-valves:finally>

</pl-valves:try-catch-finally>

</services:pipeline>

</beans:beans>
{% endhighlight %}

假设用户以URL：http://favorite.daily.taobao.net/collect_list.htm 来访问Webx应用。WebxFrameworkFilter接收请求，并且一路顺利到达pipeline。然后Pipeline开始依次执行它的valves（下面的描述略过一些相对次要的步骤）：

1. &lt;analyzeURL> - 分析URL

    分析URL的目的是取得target，这里取得的target是collect_list.htm。如果用户访问的URL中并没有提供path信息，通常被理解为：用户想要访问“主页”。AnalyzeURL valve提供了一个可选的参数“homepage”，即是在这种情况下起作用。得到的target为“homepage”。
    
    需要注意的是，target不代表模板名，也不代表类名。Target只是一个抽象的概念 —— 当前页面需要达成的目标。Target可能被后续的valves解释成模板名、类名或者其它东西。

2. 进入&lt;choose> - 多重分支

    很明显，“collect_list.htm”满足了第一个&lt;when>所附带的条件：&lt;target-extension-condition extension=" vm, jsp">，意思是target的后缀为“jsp”或为“vm”。

3. &lt;performAction> - 执行action

    和其它框架中的action概念不同，在Webx Turbine中，action是用来处理用户提交的表单的。因为本次请求未提供action参数，所以跳过该步骤。

4. &lt;performTemplateScreen> - 查找并执行screen。

    如果target-extension为”do”，则没有&lt;performTemplateScreen>这个valve。

    这里要用到一个规则：target映射成screen module类名的规则。

    假设target为xxx/yyy/zzz，那么Webx Turbine会依次查找下面的screen模块：

    > 1. screen.xxx.yyy.Zzz，

    > 2. screen.xxx.yyy.Default，

    > 3. screen.xxx.Default，

    > 4. screen.Default。

    本次请求的target为collection_list.htm，因此它会尝试查找screen.collection_list类。

    如果找到screen类，Webx Turbine就会执行它。Screen类的功能，通常是读取数据库，然后把模板所需要的对象放到TemplateContext中。

    如果找不到，也没关系 —— 这就是“页面优先”：一些页面没有业务逻辑，因此不需要screen类，只需要有模板就可以了。

5. &lt;renderTemplate> - 渲染模板

    这里用到两个规则：target映射成screen template，以及target映射成layout template。

    假设target为xxx/yyy/zzz，那么Webx Turbine会查找下面的screen模板：/templates/screen/xxx/yyy/zzz。Screen模板如果未找到，就会报404 Not Found错误。找到screen模板以后，Webx Turbine还会试着查找下面的layout模板：

    > 1. /templates/layout/xxx/yyy/zzz

    > 2. /templates/layout/xxx/yyy/default

    > 3. /templates/layout/xxx/default

    > 4. /templates/layout/default

    Layout模板如果找不到，就直接渲染screen模板；如果存在，则把渲染screen模板后的结果，嵌入到layout模板中。

   Layout模板和screen模板中，都可以调用control。每个页面只有一个screen，却可以有任意多个controls。

6. &lt;breakUnlessTargetRedirected> - 内部重定向

    在screen和action中，可以进行“内部重定向”。内部重定向实质上就是由&lt;breakUnlessTargetRedirected>实施的 —— 如果没有重定向标记，就退出；否则循环到&lt;loop>标签。

    和外部重定向不同，外部重定向是向浏览器返回一个302或303 response，其中包含Location header，浏览器看到这样的response以后，就会发出第二个请求。而内部重定向发生在pipeline内部，浏览器并不了解内部重定向。

<h2 id="velecity">Velocity部分</h2>

尚未系统学习，只是看代码做了一些了解。

1. 模板以.vm为后缀名

2. $a：美元符“$”加上java中的引用名。可以引用的对象包括内置的对象，pull Service中pull到的对象，业务处理中put进TemplateContext中的对象

3. \#set($a = $b)：声明变量并赋值；

4. \##：velocity行注释

5. \#if #elseif #else判断选择，最后用#end闭合

<h2 id="ibatis">Ibatis部分</h2>

<h3 id="ibatis_use">Ibatis的作用</h3>

Ibatis将数据库中的数据转换为java中的对象，方便C层对数据进行操作

<h3 id="ibatis_config">Ibatis的配置</h3>

1. 属性文件*-persistence.xml

2. 总配置文件SqlMap-Config.xml

3. 映射文件collection_item_mapping.xml

<h2 id="ref">参考资料</h2>