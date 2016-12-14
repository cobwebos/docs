---
title: "Azure 搜索的 API 版本 | Microsoft Docs"
description: "适用于 .NET SDK 中的 Azure 搜索 REST API 和客户端库的版本策略。"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 0458053a-164e-4682-a802-00097ecde981
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: 66aec5feaa370aef6dc4a9f83119e26ab47d726a

---

# <a name="api-versions-in-azure-search"></a>Azure 搜索中的 API 版本
Azure 搜索会定期发布功能更新。 有时，但并非总是，这些更新需要我们发布我们的 API 的新版本，以保持向后兼容性。 发布新版本使你可以控制何时以及如何在你的代码中集成搜索服务更新。

通常，我们仅在必要时发布新版本，因为它可能会促使你必须升级代码才能使用新版本 API。 仅当我们需要更改 API 的某些方面时才会发布新版本，某种程度上这会破坏向后兼容性。 这可能会因以下情况而发生：现有功能修复，或更改现有 API 外围功能的新功能。

我们遵循适用于 SDK 更新的相同规则。 Azure 搜索 SDK 遵循[语义版本控制](http://semver.org/)规则，这意味着其版本分为三个部分：主要版本号、次要版本号和内部版本号（例如，1.1.0）。 我们仅在更改破坏了向后兼容性时，才会发布 SDK 新的主要版本。 对于非破坏功能更新，我们会增大次要版本号；对于 Bug 修复，我们仅增大内部版本号。

## <a name="snapshot-of-current-versions"></a>当前版本的快照
以下是 Azure 搜索的所有编程接口的当前版本的快照。 路线图和其他详细信息可以在本文档的后续章节中找到。

| 接口 | 最新的主要版本 | 状态 |
| --- | --- | --- |
| [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) |1.1 |正式版，2016 年 2 月发布 |
| [.NET SDK 预览版](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) |2.0-preview |预览版，2016 年 8 月发布 |
| [服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) |2016-09-01 |正式版 |
| [服务 REST API 预览版](search-api-2015-02-28-preview.md) |2015-02-28-Preview |预览 |
| [管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx) |2015-08-19 |正式版 |

对于 REST API，每次调用都必须包括 `api-version`。 这使适配特定版本（如预览版 API）变得非常容易。 以下示例演示了如何指定 `api-version` 参数：

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2016-09-01

> [!NOTE]
> 尽管每个请求都带有 `api-version`，我们还是建议你对所有 API 请求使用相同版本。 当新的 API 版本引入了以前版本无法识别的属性或操作时，尤其如此。 混用 API 版本可能产生意想不到的后果，应该避免混用。
>
> 服务 REST API 和管理 REST API 的版本控制彼此独立。 版本号如有相似之处，实属巧合。
>
>

正式版（或 GA）API 可以在生产中使用，并遵循 Azure 服务级别协议。 预览版中的实验功能并不总是会迁移到 GA 版本。 **我们强烈建议不要在生产应用程序中使用预览版 API。**

## <a name="sdk-version-roadmap"></a>SDK 版本路线图
.NET SDK 的每个版本都适配服务 REST API 的特定版本。 功能首先在 REST API 中发布，然后在 SDK 中实现。

.NET SDK 现已正式发布，并且已在进行下一个版本的开发。 下表列出了 SDK 的未来版本，以便你了解接下来会发生什么。

| .NET SDK 版本 | REST API 版本 | 功能 | ETA |
| --- | --- | --- | --- |
| 1.1 |2015-02-28 |Lucene 查询语法 |2016 年 2 月 |
| 2.0-preview |2015-02-28-Preview |自定义分析器、Azure Blob 和表索引器、字段映射、ETag |2016 年 8 月 |
| 3.x |2016-09-01 |与 2.0-preview 相同，不过 Blob 索引器不支持 CSV 和 JSON 文件 |2016 年 11 月 |

## <a name="about-preview-and-generally-available-versions"></a>关于预览版和正式版
Azure 搜索始终先通过 REST API 预发布实验功能，然后再通过 .NET SDK 的预发布版本进行发布。

预览版功能并不保证会迁移到 GA 版。 鉴于 GA 版本中的功能被认为是稳定且不太可能更改（除了小的向后兼容性修复和增强），预览版功能可用于测试和实验，目的是收集有关功能设计和实现的反馈。

不过，因为预览版功能可能有所更改，我们建议不要编写依赖预览版的生产代码。 如果你使用的是旧版预览版，建议迁移到正式版 (GA)。

对于 .NET SDK：代码迁移指南可在[升级 .NET SDK](search-dotnet-sdk-migration.md) 中找到。

正式版的推出意味着 Azure 搜索现在遵循服务级别协议 (SLA)。 可以在 [Azure 搜索服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)中找到 SLA。



<!--HONumber=Nov16_HO3-->


