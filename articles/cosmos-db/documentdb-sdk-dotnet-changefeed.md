---
title: "Azure DocumentDB .NET 更改源处理器 SDK 和资源 | Microsoft Docs"
description: "了解有关更改源处理器 API 和 SDK 的全部信息，包括发布日期、停用日期和 DocumentDB .NET 更改源处理器 SDK 各版本之间的更改。"
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/14/2017
ms.author: maquaran
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 40c796bc5af1220c46950a6fac062ffdd243e59f
ms.contentlocale: zh-cn
ms.lasthandoff: 08/15/2017

---
# <a name="documentdb-net-change-feed-processor-sdk-download-and-release-notes"></a>DocumentDB .NET 更改源处理器 SDK：下载和发行说明
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET 更改源](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK 下载**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)</td></tr>

<tr><td>**API 文档**</td><td>[更改源处理器库 API 参考文档](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)</td></tr>

<tr><td>**入门**</td><td>[DocumentDB 更改源处理器 .NET SDK 入门](change-feed.md)</td></tr>

<tr><td>**当前受支持的框架**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>发行说明

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 添加了一个获取更改源中剩余待处理工作估计值的方法。
* 兼容 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.13.2 及更高版本。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* 与 [DocumentDB.NET SDK](documentdb-sdk-dotnet.md) 1.14.1 版及更低版本兼容。

## <a name="release--retirement-dates"></a>发布和停用日期
Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。 

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.1.0](#1.1.0) |2017 年 8 月 13 日 |--- |
| [1.0.0](#1.0.0) |2017 年 7 月 7 日 |--- |


## <a name="faq"></a>常见问题
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。 


