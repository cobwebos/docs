---
title: 升级到最新的 Azure 搜索服务 REST API 版本 | Microsoft 文档
description: 升级到最新的 Azure 搜索服务 REST API 版本
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 2efe7769f68988f3c0d52c8806b78c1b96d8c639
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620223"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>升级到最新的 Azure 搜索服务 REST API 版本
如果使用的是早期版本的 [Azure 搜索服务 REST API](https://docs.microsoft.com/rest/api/searchservice/)，本文有助于升级应用程序，以便使用正式发布的最新 API 版本：2017-11-11。

REST API 的版本 2017-11-11 包含某些针对早期版本进行的更改。 这些更改主要涉及向后兼容性，因此更改代码只需最小的工作量，具体取决于之前使用的是哪个版本。 请参阅[升级步骤](#UpgradeSteps)，获取有关如何更改代码以使用新 API 版本的说明。

> [!NOTE]
> Azure 搜索服务实例支持多个 REST API 版本，包括最新的版本。 可以不使用最新版本，但是我们建议迁移代码，以便使用最新版本。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>版本 2017-11-11 中的新增功能
版本 2017-11-11 是 Azure 搜索服务 REST API 正式发布的最新版本。 此 API 版本中的新功能包括：

* [同义词](search-synonyms.md) 新的同义词功能允许定义等效术语并扩大查询范围。
* [支持有效索引 textblob](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText)。 Azure Blob 索引器的全新 `text` 分析模式显著改进了索引性能。
* [服务统计 API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)。 通过此新 API 查看 Azure 搜索中当前的资源使用情况和限制。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤
如果从版本 GA、2015-02-28 或 2016-09-01 升级，除了更改版本号，可能无需对代码进行任何其他更改。 仅对于以下情况，可能需要更改代码：

* 当 API 响应中返回无法识别的属性时，代码失效。 默认情况下，应用程序应忽略无法理解的属性。
* 代码仍坚持 API 请求，并尝试将其重新发送到新 API 版本。 例如，如果应用程序仍存留从搜索 API 返回的延续标记（有关详细信息，请查找[搜索 API 参考](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)中的 `@search.nextPageParameters`）。

如果任一种情况适用，都可能需要更改相应代码。 否则，如果不是要开始使用版本 2017-11-11 的[新功能](#WhatsNew)，便无需进行任何更改。

如果从预览版 api-version 升级，上述内容同样适用，但还必须注意，某些预览功能并未在版本 2017-11-11 中提供：

* 针对包含 JSON 数组的 CSV 文件和 Blob 的 Azure Blob 存储索引器支持。
* “More like this”查询

如果代码使用这些功能，只有消除它们的使用，才能升级到 2017-11-11。

> [!IMPORTANT]
> 请记住，预览版 API 仅供测试和评估，不应在生产环境中使用。
> 
> 

## <a name="conclusion"></a>结束语
如果需要有关使用 Azure 搜索服务 REST API 的更多详细信息，请参阅 MSDN 上最近更新的 [API 参考](https://docs.microsoft.com/rest/api/searchservice/)。

我们欢迎你对 Azure 搜索提出反馈。 如果遇到问题，请随时通过 [Azure 搜索 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch)或 [StackOverflow](http://stackoverflow.com/) 向我们寻求帮助。 如果要在 StackOverflow 上询问有关 Azure 搜索的问题，请确保使用 `azure-search` 标记问题。

感谢使用 Azure 搜索！

