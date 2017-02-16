---
title: "升级到 Azure 搜索服务 REST API 版本 2016-09-01 | Microsoft Docs"
description: "升级到 Azure 搜索服务 REST API 版本 2016-09-01"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 7d45759915f38ba4337b745eb2b28dcbc72dbbe0
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f

---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>升级到 Azure 搜索服务 REST API 版本 2016-09-01
如果使用的是 [Azure 搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的版本 2015-02-28 或 2015-02-28-Preview，本文有助于你升级应用程序，以便使用接下来正式发布的 API 版本：2016-09-01。

REST API 的版本 2016-09-01 包含某些针对早期版本进行的更改。 这些更改主要涉及向后兼容性，因此更改代码只需最小的工作量，具体取决于之前使用的是哪个版本。 请参阅[升级步骤](#UpgradeSteps)，获取有关如何更改代码以使用新 API 版本的说明。

> [!NOTE]
> Azure 搜索服务实例支持多个 REST API 版本，包括最新的版本。 你可以不使用最新版本，但是我们建议迁移你的代码，以便使用最新版本。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>版本 2016-09-01 中的新增功能
版本 2016-09-01 是 Azure 搜索服务 REST API 的第二个正式发布的版本。 此 API 版本中的新功能包括：

* [自定义分析器](https://aka.ms/customanalyzers)，使你能够控制将文本转换为可索引且可搜索标记的过程。
* [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)和 [Azure 表存储](search-howto-indexing-azure-tables.md)索引器，使你能够轻松地根据计划或需求将数据从 Azure 存储导入 Azure 搜索。
* [字段映射](search-indexer-field-mappings.md)，使你能够自定义索引器将数据导入 Azure 搜索的方式。
* ETag，使你能够以并发安全的方式更新索引、索引器和数据源的定义。 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤
如果从版本 2015-02-28 升级，除了更改版本号，可能无需对代码进行任何其他更改。 仅对于以下情况，可能需要更改代码：

* 当 API 响应中返回无法识别的属性时，代码失效。 默认情况下，你的应用程序应忽略无法理解的属性。
* 代码仍坚持 API 请求，并尝试将其重新发送到新 API 版本。 例如，如果应用程序仍存留从搜索 API 返回的延续标记（有关详细信息，请查找[搜索 API 参考](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)中的 `@search.nextPageParameters`）。

如果任一种情况适用，都可能需要更改相应代码。 否则，如果不是要开始使用版本 2016-09-01 的[新功能](#WhatsNew)，便无需进行任何更改。

如果从版本 2015-02-28-Preview 升级，上述内容同样适用，但还必须注意，某些预览功能并未在版本 2016-09-01 中提供：

* 针对包含 JSON 数组的 CSV 文件和 Blob 的 Azure Blob 存储索引器支持。
* 同义词
* “More like this”查询

如果代码使用这些功能，只有消除它们的使用，才能升级到 2016-09-01。

> [!IMPORTANT]
> 请记住，预览版 API 仅供测试和评估，不应在生产环境中使用。
> 
> 

## <a name="conclusion"></a>结束语
如果需要有关使用 Azure 搜索服务 REST API 的更多详细信息，请参阅 MSDN 上最近更新的 [API 参考](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

我们欢迎你对 Azure 搜索提出反馈。 如果遇到问题，请随时通过 [Azure 搜索 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch)或 [StackOverflow](http://stackoverflow.com/) 向我们寻求帮助。 如果你要在 StackOverflow 上询问有关 Azure 搜索的问题，请确保使用 `azure-search` 标记问题。

感谢使用 Azure 搜索！




<!--HONumber=Jan17_HO2-->


