---
title: "在 Azure 内容交付网络中管理 Web 内容的到期时间 | Microsoft Docs"
description: "了解如何管理 Azure CDN 中 Azure Web 应用/云服务、ASP.NET 或 IIS 内容的过期问题。"
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>在 Azure 内容交付网络中管理 Web 内容的到期时间
 在 Azure CDN 中
> [!div class="op_single_selector"]
> * [Azure Web 应用/云服务、ASP.NET 或 IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob 存储](cdn-manage-expiration-of-blob-content.md)
> 

来自任何可公开访问的源 Web 服务器的文件均可在 Azure 内容交付网络 (CDN) 中进行缓存，直到其生存时间 (TTL) 结束。 TTL 由来自源服务器的 HTTP 响应中的 [`Cache-Control` 标头](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)决定。 本文介绍如何设置 Microsoft Azure 应用服务的 Web 应用功能、Azure 云服务、ASP.NET 应用程序和 Internet Information Services 网站的 `Cache-Control` 标头，所有标头的配置方式都类似。

> [!TIP]
> 可以选择不对文件设置 TTL。 在这种情况下，Azure CDN 会自动应用默认为 7 天的 TTL。
> 
> 有关 Azure CDN 如何加速访问文件和其他资源的详细信息，请参阅 [Azure 内容交付网络概述](cdn-overview.md)。
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>在配置文件中设置 Cache-Control 标头
对于静态内容（如图像和样式表），可以通过修改 Web 应用程序的 **applicationHost.config** 或 **web.config** 文件来控制更新频率。 配置文件中的 **System.webServer\staticContent\clientCache** 元素将设置内容的 `Cache-Control` 标头。 对于 **web.config** 文件，配置设置将影响文件夹及所有子文件夹中的所有内容，除非设置在子文件夹级别被重写。 例如，可以在根目录设置一个默认的 TTL 设置，以将所有静态内容缓存三天，并将包含更易变内容的子文件夹设置为将其内容仅缓存六个小时。 尽管 **applicationHost.config** 文件设置会影响站点上的所有应用程序，但可以通过在应用程序中任何现有 **web.config** 文件的设置重写这些设置。

以下 XML 示例演示如何通过设置 **clientCache** 将最大有效期指定为 3 天：  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

指定 **UseMaxAge** 时，会根据 **CacheControlMaxAge** 属性中指定的值将 `Cache-Control: max-age=<nnn>` 标头添加到响应中。 **cacheControlMaxAge** 属性的时间跨度的格式为 `<days>.<hours>:<min>:<sec>`。 有关 **clientCache** 节点的详细信息，请参阅[客户端缓存 <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。  

## <a name="setting-cache-control-headers-in-code"></a>在代码中设置 Cache-Control 标头
对于 ASP.NET 应用程序，可以通过设置 **HttpResponse.Cache** 属性，以编程方式控制 CDN 缓存行为。 有关 **HttpResponse.Cache** 属性的详细信息，请参阅 [HttpResponse.Cache 属性](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)和 [HttpCachePolicy 类](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

若要以编程方式在 ASP.NET 中缓存应用程序内容，请执行以下步骤：
   1. 验证是否已通过将 `HttpCacheability` 设置为 *Public* 将内容标记为可缓存。 
   2. 调用以下方法之一设置缓存验证器：
      - 调用 `SetLastModified` 设置 LastModified 时间戳。
      - 调用 `SetETag` 设置 `ETag` 值。
   3. （可选）调用 `SetExpires` 指定缓存到期时间。 否则，将应用本文前面所述的默认缓存试探程序。

例如，要将内容缓存 1 小时，可添加以下 C# 代码：  

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

