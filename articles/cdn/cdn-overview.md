<properties 
	pageTitle="Azure CDN 概述" 
	description="了解什么是 Azure 内容交付网络 (CDN)，以及如何使用它通过缓存 blob 和静态内容来交付高带宽内容。" 
	services="cdn" 
	documentationCenter=".NET" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.date="09/01/2015" 
	wacn.date=""/>

#Azure 内容交付网络 (CDN) 概述

Azure 内容交付网络 (CDN) 将云服务所使用的 Azure blob 和静态内容缓存在按特定策略布置好的位置，以便提供最大的带宽，方便将内容交付给用户。

如果你已经是 CDN 客户，则现在就可以通过 [Microsoft Azure 管理门户](https://manage.windowsazure.cn)管理你的 CDN 终结点。


>[AZURE.NOTE]Azure CDN 有独立的[收费计划](http://www.windowsazure.cn/pricing/)，不同于Azure 存储空间或 Azure 云服务。
 

CDN 为开发人员提供了一个全局解决方案，通过在世界各地的物理节点缓存内容来交付高带宽内容。如需 CDN 节点位置的最新列表，请参阅 [Azure 内容交付网络 (CDN) POP 位置](http://msdn.microsoft.com/library/azure/gg680302.aspx)。

使用 CDN 来缓存 Azure 数据的优点包括：

- 为远离内容源的最终用户提供更好的性能和用户体验，在使用的应用程序中需要很多“互联网往返”来加载内容
- 大规模分布以更好地处理瞬间的高负载，例如在产品发布活动开始时出现的高负载。 


>[AZURE.IMPORTANT]创建或启用 CDN 终结点时，可能需要长达 60 分钟的时间才能完成全球传播。
 
首次向 CDN 请求对象时，将直接从 Blob 服务或云服务检索该对象。使用 CDN 语法来发出请求时，该请求会重定向到最接近请求发出位置的 CDN 终结点，以方便访问对象。如果在终结点上找不到对象，则会从服务检索该对象并将其缓存在终结点上，其中会保留针对已缓存对象的生存时间 (TTL) 设置。
 
##缓存 Azure 存储空间的内容

一旦在 Azure 存储帐户上启用 CDN，则会通过 CDN 缓存任何位于公共容器中且可供匿名访问的 blob。只能通过 Azure CDN 缓存可公开访问的 blob。若要使某个 blob 接受公开匿名访问，必须将其容器指定为公开。这样操作以后，该容器中的所有 blob 都将可供匿名读取访问。你也可以让容器数据可供公开访问，或者只允许访问容器中的 blob。请参阅[限制对容器和 Blob 的访问](http://msdn.microsoft.com/zh-cn//library/azure/dd179354.aspx)，了解如何管理容器和 blob 的访问控制。

为了获得最佳性能，可以使用 CDN 边缘缓存来交付不到 10 GB 大小的 blob。

为存储帐户启用 CDN 访问时，管理门户会为你提供以下格式的 CDN 域名：http://<identifier>.vo.msecnd.net/。此域名可用于访问公共容器中的 blob。例如，如果名为 myaccount 的存储帐户中存在名为 music 的公共容器，用户就可以使用以下两个 URL 中的一个来访问该容器中的 blob：

- **Azure Blob 服务 URL**：`http://myAccount.blob.core.windows.net/music/` 
- **Azure CDN URL**：`http://<identifier>.vo.msecnd.net/music/` 

##缓存 Azure 网站的内容

你可以让网站的 CDN 缓存 Web 内容，例如图像、脚本和样式表。请参阅[将 Azure 网站与 Azure CDN 集成](/documentation/articles/cdn-websites-with-cdn)。

为网站启用 CDN 访问时，管理门户会为你提供以下格式的 CDN 域名：http://<identifier>.vo.msecnd.net/。此域名可用于从网站中检索对象。例如，如果公共容器的名称为 cdn，图像文件名为 music.png，则用户可以使用下列两个 URL 中的一个来访问该对象：

- **Azure 网站 URL**：`http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Azure CDN URL**：`http://<identifier>.vo.msecnd.net/cdn/music.png`
 
##缓存 Azure 云服务的内容

你可以将对象缓存到 Azure 云服务提供的 CDN。

云服务的缓存具有以下限制：


- CDN 只应用于缓存静态内容。

	>[AZURE.WARNING]缓存易失性很高的或动态性很强的内容可能会对性能造成负面影响，或者会导致内容问题，这些都会导致成本上升。
- 你的云服务必须进行生产部署。
- 你的云服务必须使用 HTTP 在端口 80 上提供对象。
- 云服务必须将要缓存或交付的内容放置在云服务的 /cdn 文件夹中。

为云服务启用 CDN 访问时，管理门户会为你提供以下格式的 CDN 域名：http://<identifier>.vo.msecnd.net/。此域名可用于从云服务中检索对象。例如，如果云服务名为 myHostedService，交付内容的 ASP.NET 网页名为 music.aspx，则用户可以使用下列两个 URL 中的一个来访问该对象：


- **Azure 云服务 URL**：`http://myHostedService.chinacloudapp.cn/cdn/music.aspx` 
- **Azure CDN URL**：`http://<identifier>.vo.msecnd.net/music.aspx` 


###使用查询字符串缓存特定内容

你可以使用查询字符串来区分从云服务检索的对象。例如，如果云服务显示一个可以改变的图表，你就可以传递一个查询字符串来检索所需的特定图表。例如：

`http://<identifier>.vo.msecnd.net/chart.aspx?item=1`

查询字符串以字符串文字形式传递。如果你有一个使用两个参数的服务（例如 `?area=2&item=1`），并随后使用 `?item=1&area=2` 来调用该服务，则会缓存同一对象的两个副本。
 

##通过 HTTPS 访问缓存的内容


Azure 允许你通过 HTTPS 调用从 CDN 检索内容。这样你就可以将缓存在 CDN 中的内容整合到安全网页中，不会收到混合使用安全内容类型的警告。

使用 HTTPS 访问 CDN 内容具有以下限制：


- 必须使用 CDN 提供的证书。不支持第三方证书。
- 必须使用 CDN 域来访问内容。由于 CDN 目前不支持自定义证书，HTTPS 支持不适用于自定义域名 (CNAME)。



即使启用了 HTTPS，你也可以使用 HTTP 和 HTTPS 检索 CDN 中的内容。

有关对 CDN 内容启用 HTTPS 的详细信息，请参阅[如何对 Azure 启用内容交付网络 (CDN)](http://msdn.microsoft.com/zh-cn/library/azure/gg680301.aspx)。


##使用自定义域访问缓存的内容

你可以将 CDN HTTP 终结点映射到自定义域名，并使用该名称请求 CDN 中的对象。

有关如何映射自定义域的详细信息，请参阅[如何将内容交付网络 (CDN) 内容映射到自定义域](http://msdn.microsoft.com/zh-cn/library/azure/gg680307.aspx)。

 

<!---HONumber=74-->