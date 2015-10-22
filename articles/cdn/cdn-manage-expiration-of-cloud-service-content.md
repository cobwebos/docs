<properties 
 pageTitle="如何在 Azure 内容交付网络 (CDN) 中管理云服务内容的过期问题" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.date="09/01/2015" 
 wacn.date=""/>

#如何在 Azure 内容交付网络 (CDN) 中管理云服务内容的过期问题

最得益于 Azure CDN 缓存的对象是那些在其生存时间 (TTL) 内被访问最频繁的对象。对象在其生存时间 (TTL) 内会始终保留在缓存中，在该时间过后会从云服务进行刷新。随后，此过程将重复。

如果未提供缓存值，则对象的 TTL 为 7 天。

对于图像和样式表之类的静态内容，你可以控制更新频率，方法是在包含该内容的 CDN 文件夹中包括 web.config，然后修改 **clientCache** 设置以控制内容的 Cache-Control 标头。该 web.config 设置会影响文件夹和所有子文件夹中的所有内容，除非在更下面的子文件夹中对设置进行了重写。例如，你可以在根目录设置一个默认的生存时间，允许所有静态内容缓存 3 天，但同时，你可以在包含更多易变内容的子文件夹中将缓存时间设置为 6 小时。

以下 XML 显示了一个示例，该示例通过设置 **clientCache**，将最大有效期指定为 3 天。

	<configuration> 
	  <system.webServer> 
	        <staticContent> 
	            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
	        </staticContent> 
	  </system.webServer> 
	</configuration>

指定 **UseMaxAge** 时，会根据 **CacheControlMaxAge** 属性中指定的值将 Cache-Control: max-age=<nnn> 标头添加到响应中。**cacheControlMaxAge** 属性的时间跨度的格式为 <days>.<hours>:<min>:<sec>。有关 **clientCache** 节点的详细信息，请参阅[客户端缓存 <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。

对于从 .aspx 页面之类的应用程序返回的内容，你可以通过编程方式来设置 **HttpResponse.Cache** 属性，从而设置 CDN 缓存行为。有关 **HttpResponse.Cache** 属性的详细信息，请参阅 [HttpResponse.Cache 属性](http://msdn.microsoft.com/zh-cn/library/system.web.httpresponse.cache.aspx)和 [HttpCachePolicy 类](http://msdn.microsoft.com/zh-cn/library/system.web.httpcachepolicy.aspx)。

如果需要通过编程方式来缓存应用程序内容，请确保你已将 HttpCacheability 设置为 *Public*，从而将内容标记为可缓存。另请确保设置缓存验证标记。缓存验证标记可以是“上次修改时间”时间戳（通过调用 SetLastModified 来设置），也可以是 etag 值（通过调用 SetETag 来设置）。你还可以通过调用 SetExpires 来选择性地指定缓存到期时间，或者依靠本文前面介绍过的默认缓存试探程序。

例如，若要将内容缓存 1 小时，可添加以下语句：

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

##另请参阅

[如何在 Azure 内容交付网络 (CDN) 中管理 Bob 内容的过期问题](/documentation/articles/cdn-manage-expiration-of-blob-content)

<!---HONumber=74-->