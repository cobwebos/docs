<properties 
	pageTitle="如何使用 CDN | Microsoft Azure" 
	description="了解如何使用 Azure 内容交付网络 (CDN) 通过缓存 blob 和静态内容来交付高带宽内容。" 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.date="09/01/2015" 
	wacn.date=""/>


# 使用 Azure CDN

Azure 内容交付网络 (CDN) 是在 Azure 中对 HTTP 应用程序进行缩放的基础。它为 Azure 客户提供了一个全局解决方案，可以缓存和交付靠近最终用户的内容。这样一来，就可以将用户请求智能地路由到性能最佳的 CDN 边缘 POP，不必每次都转到源。这可以显著改善性能和用户体验。有关 CDN 节点位置的当前列表，请参阅 [Azure CDN 节点位置]。

使用 CDN 来缓存 Azure 数据的优点包括：

-   为远离内容源的最终用户提供更好的性能和用户体验，在使用的应用程序中需要很多“互联网往返”来加载内容
-   大规模分布以更好地处理瞬间的高负载，例如在产品发布活动开始时
-   通过分发用户请求并通过全局边缘 POP 来提供内容，可以减少发送到源的流量，从而减轻源的负荷。

现有 CDN 客户当前可在 [Azure 管理门户]中使用 Azure CDN。CDN 是你的订阅的一项附加功能，具有单独的[计费计划]。

##步骤 1：在 Azure 中创建 CDN 源

CDN 源是一个位置，CDN 从该位置提取内容并将内容缓存在边缘 POP 上。集成 Azure 源包括 Azure 应用、云服务、存储和媒体服务。

## 步骤 2：创建指向 Azure 源的 CDN 终结点

一旦设置了源，则当你[创建新的 CDN 终结点](/documentation/articles/cdn-create-new-endpoint)时，该源就会出现在源列表中。

> [AZURE.NOTE]为终结点创建的配置将不能立即可用；最多需要 60 分钟时间进行注册以便通过 CDN 网络传播。尝试立即使用 CDN 域名的用户可能会收到状态代码 400（错误请求），直到可通过 CDN 访问该内容。

## 步骤 3：设置 CDN 配置 

你可以为 CDN 终结点启用大量功能，例如缓存策略、查询字符串缓存等。

## 步骤 4：访问 CDN 内容

若要访问 CDN 上的缓存内容，请使用门户中提供的 CDN URL。例如，缓存的 blob 的地址将类似于：http://<*CDNNamespace*>.vo.msecnd.net/<*myPublicContainer*>/<*BlobName*>



## 另请参阅

[Azure 内容交付网络 (CDN) 概述](/documentation/articles/cdn-overview)
 

<!---HONumber=74-->