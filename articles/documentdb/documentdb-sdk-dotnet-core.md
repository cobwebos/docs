---
title: "DocumentDB .NET Core API 和 SDK | Microsoft Docs"
description: "了解有关 .NET Core API 和 SDK 的全部信息，包括发布日期、停用日期和 DocumentDB .NET Core SDK 各版本之间的更改。"
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2017
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: 855fd05d9addc2e57568067e4d434836ceeed570
ms.openlocfilehash: 21a5f321bd1a3b45cd87c2c9274139c47562cee7


---
# <a name="documentdb-apis-and-sdks"></a>DocumentDB API 和 SDK
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-net-core-api-and-sdk"></a>DocumentDB .NET Core API 和 SDK
<table>

<tr><td>**SDK 下载**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API 文档**</td><td>[ 参考文档](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**示例**</td><td>[.NET代码示例](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**入门**</td><td>[开始使用 DocumentDB .NET Core SDK](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Web 应用教程**</td><td>[使用 DocumentDB 开发 Web 应用程序](documentdb-dotnet-application.md)</td></tr>

<tr><td>**当前受支持的框架**</td><td>[.NET 标准 1.6](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>发行说明

DocumentDB .NET Core SDK 具有与最新版 [DocumentDB.NET SDK](documentdb-sdk-dotnet.md) 相同的功能。

> [!NOTE] 
> DocumentDB .NET Core SDK 与通用 Windows 平台 (UWP) 应用尚不兼容。 如需支持 UWP 应用的 .NET Core SDK 预览版，请向 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) 发送电子邮件。

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.0.0)

通过 DocumentDB .NET Core SDK，可构建能在 Windows、Mac 和 Linux 上快速运行的跨平台 [ASP.NET Core](https://www.asp.net/core) 和 [.NET Core](https://www.microsoft.com/net/core#windows) 应用。 DocumentDB .NET Core SDK 的最新版本完全兼容 [Xamarin](https://www.xamarin.com)，可用于生成面向 iOS、Android 和 Mono (Linux) 的应用程序。  

### <a name="a-name010-preview010-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentdbcore010-preview"></a><a name="0.1.0-preview"/>[0.1.0-preview](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/0.1.0-preview)

通过 DocumentDB .NET Core 预览版 SDK，可构建能在 Windows、Mac 和 Linux 上快速运行的跨平台 [ASP.NET Core](https://www.asp.net/core) 和 [.NET Core](https://www.microsoft.com/net/core#windows) 应用。

DocumentDB .NET Core 预览版 SDK 与最新版 [DocumentDB.NET SDK](documentdb-sdk-dotnet.md) 功能相同，并支持以下内容：
* 所有[连接模式](documentdb-performance-tips.md#networking)：网关模式、Direct TCP 和 Direct HTTP。 
* 所有[一致性级别](documentdb-consistency-levels.md)：强一致性、会话一致性、有限过期一致性和最终一致性。
* [已分区集合](documentdb-partition-data.md)。 
* [多区域数据库帐户和异地复制](documentdb-distribute-data-globally.md)。

若有与此 SDK 相关的问题，请发布到 [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)，或在 [github 存储库](https://github.com/Azure/azure-documentdb-dotnet/issues)中提出问题。 

## <a name="release--retirement-dates"></a>发布和停用日期

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.0.0](#1.0.0) |2016 年 12 月 21 日 |--- |
| [0.1.0-preview](#0.1.0-preview) |2016 年 11 月 15 日 |2016 年 12 月 31 日 |

## <a name="see-also"></a>另请参阅
要了解有关 DocumentDB 的详细信息，请参阅 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服务页。 




<!--HONumber=Jan17_HO2-->


