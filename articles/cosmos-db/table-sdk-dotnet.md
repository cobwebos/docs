---
title: "Azure CosmosDB 表 API .NET SDK 和资源 | Microsoft Docs"
description: "了解有关 Azure Cosmos DB 表 API 的全部信息，包括发布日期、停用日期和各版本之间进行的更改。"
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/31/2018
ms.author: mimig
ms.openlocfilehash: 9083ae631f654789b621a1fc62e0b9d31c24f119
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB 表 .NET API：下载和发行说明
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 下载**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API 文档**|[ 参考文档](https://aka.ms/acdbtableapiref)|
|**快速入门**|[Azure Cosmos DB: Build an app with .NET and the Table API](create-table-dotnet.md)（Azure Cosmos DB：使用 .NET 和表 API 生成应用）|
|**教程**|[Azure Cosmos DB：在 .NET 中使用表 API 进行开发](tutorial-develop-table-dotnet.md)|
|**当前受支持的框架**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> 如果已在预览期间创建表 API 帐户，请[新建表 API 帐户](create-table-dotnet.md#create-a-database-account)，这样才能使用正式版表 API SDK。
>

## <a name="release-notes"></a>发行说明

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 将 TableQueryMaxItemCount、TableQueryEnableScan、TableQueryMaxDegreeOfParallelism 和 TableQueryContinuationTokenLimitInKb 添加到 TableRequestOptions
* Bug 修复

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 正式发布版

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview
* 初始预览版

## <a name="release-and-retirement-dates"></a>发布日期和停用日期
Microsoft 至少会在停用 SDK 前提前 12 个月发出通知，以便顺利转换为更高版本/受支持版本。

[WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) 预览包已弃用，已被 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 包取代。 WindowsAzure.Storage-PremiumTable SDK 将在 2018 年 11 月 15 日停用，到时，将不允许向已停用的 SDK 发出请求。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。 

使用已停用的 SDK 对 Azure Cosmos DB 发出的任何请求都会遭服务拒绝。
<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.0.0](#1.0.0) |2017 年 11 月 15 日|--- |
| [0.9.0-preview](#0.9.0-preview) |2017 年 11 月 11 日 |--- |

## <a name="troubleshooting"></a>故障排除

如果在尝试使用 Microsoft.Azure.CosmosDB.Table NuGet 包时看到以下错误： 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

可以使用下列两种方法之一解决此问题：

* 使用包管理器控制台安装 Microsoft.Azure.CosmosDB.Table 包及其依赖项。 为此，请在解决方案的包管理器控制台中键入以下代码。 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* 使用首选 Nuget 包管理工具先安装 Microsoft.Azure.Storage.Common Nuget 包，再安装 Microsoft.Azure.CosmosDB.Table。

## <a name="faq"></a>常见问题

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Azure Cosmos DB 表 API 的详细信息，请参阅 [Azure Cosmos DB 表 API 简介](table-introduction.md)。 
