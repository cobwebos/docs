<properties linkid="video-center-index" urlDisplayName="index" pageTitle="将 ASP.NET 会话状态用于 Azure 网站" metaKeywords="azure cache service session state" description="了解如何使用 Azure 缓存服务来支持 ASP.NET 会话状态缓存。" metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure  Websites" authors="jroth" solutions="" manager="" editor="mollybos" />




# 如何将 ASP.NET 会话状态用于 Azure 网站

*[Rick Anderson] 撰写。2014 年 7 月 1 日更新。*

本主题说明如何将 Azure Redis Cache 服务（预览版）用于会话状态。

如果你的 ASP.NET Web 应用程序使用会话状态，则你需要设置外部会话状态提供程序（可为 Redis Cache 服务或 SQL Server 会话状态提供程序）。如果你使用会话状态，但未使用外部提供程序，则你的 Web 应用程序只能限定为一个实例。Redis Cache 服务是最快最简单的启用方式。

将缓存服务（预览版）用于会话状态缓存的基本步骤包括：

* [创建缓存。](#createcache)
* [将 RedisSessionStateProvider NuGet 包添加到 Web 应用程序。](#configureproject)
* [修改 Web.config 文件。](#configurewebconfig)
* [使用会话对象存储和检索缓存项。](#usesessionobject)

<h2><a id="createcache"></a>创建缓存</h2>
遵照[这些指示](/zh-cn/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache)创建缓存。

<h2><a id="configureproject"></a>将 RedisSessionStateProvider NuGet 包添加到 Web 应用程序</h2>
安装 NuGet `RedisSessionStateProvider` 包。从 Package Manager Console（"工具">"NuGet Package Manager">"Package Manager Console"）使用以下命令进行安装：

  `PM> Install-Package RedisSessionStateProvider -IncludePrerelease`
  
若要从"工具">"NuGet Package Manager">"Manage NugGet Packages for Solution"进行安装，请搜索 `RedisSessionStateProvider` 并确保指定"包括预发行版"。

有关详细信息，请参阅 [NuGet RedisSessionStateProvider 页](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ )和[配置缓存客户端](/zh-cn/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet)。

<h2><a id="configurewebconfig"></a>修改 Web.Config 文件</h2>
除了为缓存生成程序集引用，NuGet 包还在 *web.config* 文件中添加存根项。 

1. 打开 *web.config* 并查找 **sessionState** 元素。

1. 输入 `host`、 `accessKey`、 `port` 的值（SSL 端口应为 6380），并将 `SSL` 设置为 `true`。可以从 Azure 管理预览门户边栏选项卡为你的缓存实例获取这些值。有关详细信息，请参阅[连接到缓存](/zh-cn/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache)。
以下标记显示了对 *web.config* 文件所做的更改。


  <pre class="prettyprint">  
    &lt;system.web&gt;
    &lt;customErrors mode="Off" /&gt;
    &lt;authentication mode="None" /&gt;
    &lt;compilation debug="true" targetFramework="4.5" /&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
  &lt;sessionState mode="Custom" customProvider="RedisSessionProvider"&gt;
      &lt;providers&gt;  
          &lt;!--&lt;add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          /&gt;--&gt;
         &lt;add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.chinacloudapi.cn" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> /&gt;
      &lt;!--&lt;add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" /&gt;--&gt;
      &lt;/providers&gt;
    &lt;/sessionState&gt;
  &lt;/system.web&gt;</pre>


<h2><a id="usesessionobject"></a>在代码中使用 Session 对象</h2>
最后一步是开始在 ASP.NET 代码中使用 Session 对象。通过使用 **Session.Add** 方法将对象添加到会话状态。此方法使用键值对在会话状态缓存中存储项。

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

下面的代码从会话状态中检索该值。

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

还可以使用 Redis Cache 在 Web 应用程序中缓存对象。有关详细信息，请参阅 [15 分钟学会创建包含 Azure Redis Cache 的 MVC 影片应用程序。](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/)。
有关如何使用 ASP.NET 会话状态的详细信息，请参阅 [ASP.NET 会话状态概述][]。

  
  
  [已安装最新的]: /zh-cn/downloads/?sdk=net  
  [ASP.NET 会话状态概述]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
