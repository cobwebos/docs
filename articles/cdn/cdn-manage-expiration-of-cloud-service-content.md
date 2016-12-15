---
title: "如何管理 Azure CDN 中 Azure Web 应用/云服务、ASP.NET 和 IIS 内容的过期问题 | Microsoft Docs"
description: "描述如何管理 Azure CDN 中云服务内容的过期问题"
services: cdn
documentationcenter: .NET
author: camsoper
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/19/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 61c14a27fc82b128dec41e2ed46850ac7fb554ad


---
# <a name="how-to-manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>如何管理 Azure CDN 中 Azure Web 应用/云服务、ASP.NET 或 IIS 内容的过期问题 |Microsoft Azure
> [!div class="op_single_selector"]
> * [Azure Web 应用/云服务、ASP.NET 或 IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure 存储 blob 服务](cdn-manage-expiration-of-blob-content.md)
> 
> 

来自任何可公开访问的源 Web 服务器的文件均可在 Azure CDN 中进行缓存，直到其生存时间 (TTL) 结束。  TTL 由来自源服务器的 HTTP 响应中的 [*Cache-Control* 标头](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)决定。  本文描述如何设置 `Cache-Control`Azure Web 应用、Azure 云服务、ASP.NET 应用程序和 Internet Information Services 网站的标头，所有标头的配置方式都类似。

> [!TIP]
> 可以选择不对文件设置 TTL。  在这种情况下，Azure CDN 将自动应用默认为 7 天的 TTL。
> 
> 有关 Azure CDN 如何加速访问文件和其他资源的详细信息，请参阅 [Azure CDN 概述](cdn-overview.md)。
> 
> 

## <a name="setting-cache-control-headers-in-configuration"></a>在配置中设置 Cache-Control 标头
对于静态内容（如图像和样式表），可以通过修改 Web 应用程序的 **applicationHost.config** 或 **web.config** 文件来控制更新频率。  配置文件中的 **System.webServer\staticContent\clientCache** 元素将设置内容的 `Cache-Control` 标头。 对于 **web.config**，配置设置将影响文件夹及所有子文件夹中的所有内容，除非设置在子文件夹级别被重写。  例如，你可以在根目录设置一个默认的生存时间，允许所有静态内容缓存 3 天，但同时，你可以在包含更多易变内容的子文件夹中将缓存时间设置为 6 小时。  对于 **applicationHost.config**，站点上的所有应用程序都将受到影响，但可以在应用程序的 **web.config** 文件中重写。

以下 XML 显示了一个示例，该示例通过设置 **clientCache**，将最大有效期指定为 3 天。  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

指定 **UseMaxAge** 时，会根据 **CacheControlMaxAge** 属性中指定的值将 `Cache-Control: max-age=<nnn>` 标头添加到响应中。 **cacheControlMaxAge** 属性的时间跨度的格式为 <days>.<hours>:<min>:<sec>。 有关 **clientCache** 节点的详细信息，请参阅[客户端缓存 <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。  

## <a name="setting-cache-control-headers-in-code"></a>在代码中设置 Cache-Control 标头
对于 ASP.NET 应用程序，可以通过设置 **HttpResponse.Cache** 属性，即可以编程方式设置 CDN 缓存行为。 有关 **HttpResponse.Cache** 属性的详细信息，请参阅 [HttpResponse.Cache 属性](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)和 [HttpCachePolicy 类](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

如果需要在 ASP.NET 中以编程方式缓存应用程序内容，请确保已将 HttpCacheability 设置为 *Public*，从而将内容标记为可缓存。 另请确保设置缓存验证标记。 缓存验证标记可以是“上次修改时间”时间戳（通过调用 SetLastModified 来设置），也可以是 etag 值（通过调用 SetETag 来设置）。 你还可以通过调用 SetExpires 来选择性地指定缓存到期时间，或者依靠本文前面介绍过的默认缓存试探程序。  

例如，若要将内容缓存 1 小时，可添加以下语句：  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>后续步骤
* [阅读有关 **clientCache** 元素](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)的详细信息
* [阅读有关 **HttpResponse.Cache** 属性的文档](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [阅读有关 **HttpCachePolicy 类**的文档](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  




<!--HONumber=Nov16_HO3-->


