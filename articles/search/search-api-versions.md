---
title: Azure 搜索的 API 版本 | Microsoft Docs
description: 适用于 .NET SDK 中的 Azure 搜索 REST API 和客户端库的版本策略。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: brjohnst
ms.openlocfilehash: 8d1e30b0bca3c63fe4528c06e5389d8cbe27a7e6
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113599"
---
# <a name="api-versions-in-azure-search"></a>Azure 搜索中的 API 版本
Azure 搜索会定期发布功能更新。 这些更新有时（但并非总是）需要发布 API 的新版本，以保持后向兼容性。 发布新版本使你可以控制何时以及如何在代码中集成搜索服务更新。

通常，Azure 搜索团队仅在必要时发布新版本，因为它可能会促使你必须升级代码才能使用新版本 API。 仅在 API 的某些方面以破坏后向兼容性的方式发生更改时，才需要新版本。 此类更改可能会因以下情况而发生：现有功能修复，或更改现有 API 外围功能的新功能。

相同规则适用于 SDK 更新。 Azure 搜索 SDK 遵循[语义版本控制](http://semver.org/)规则，这意味着其版本分为三个部分：主要版本号、次要版本号和内部版本号（例如，1.1.0）。 仅在更改破坏了后向兼容性时，才会发布新的 SDK 主版本。 非破坏功能更新会增大次要版本号，Bug 修复仅增大内部版本号。

> [!NOTE]
> Azure 搜索服务实例支持多个 REST API 版本，包括最新的版本。 可以不使用最新版本，但是我们建议迁移代码，以便使用最新版本。 使用 REST API 时，必须在每个请求中通过 api-version 参数指定 API 版本。 使用 .NET SDK 时，使用的 SDK 版本确定对应的 REST API 版本。 如果使用较旧的 SDK，即使已升级服务以支持较新的 API 版本，也可以继续运行代码，而不进行任何更改。

## <a name="snapshot-of-current-versions"></a>当前版本的快照
以下是 Azure 搜索的所有编程接口的当前版本的快照。

| 接口 | 最新的主要版本 | 状态 |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |正式版，2018 年 4 月发布 |
| [.NET SDK 预览版](https://aka.ms/search-sdk-preview) |4.0.1 - 预览版 |预览版，2017 年 5 月发布 |
| [服务 REST API](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |正式版 |
| [服务 REST API 2017-11-11-预览版](search-api-2017-11-11-preview.md) |2017-11-11-预览版 |预览 |
| [.NET 管理 SDK](https://aka.ms/search-mgmt-sdk) |2.0 |正式版 |
| [管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |正式版 |

对于 REST API，每次调用都必须包括 `api-version`。 使用 `api-version` 使适配特定版本（如预览版 API）变得非常容易。 以下示例演示了如何指定 `api-version` 参数：

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> 尽管每个请求都带有 `api-version`，我们还是建议对所有 API 请求使用相同版本。 当新的 API 版本引入了以前版本无法识别的属性或操作时，尤其如此。 混用 API 版本可能产生意想不到的后果，应该避免混用。
>
> 服务 REST API 和管理 REST API 的版本控制彼此独立。 版本号如有相似之处，实属巧合。

正式版（或 GA）API 可以在生产中使用，并遵循 Azure 服务级别协议。 预览版中的实验功能并不总是会迁移到 GA 版本。 强烈建议不要在生产应用程序中使用预览版 API。

## <a name="about-preview-and-generally-available-versions"></a>关于预览版和正式版
Azure 搜索始终先通过 REST API 预发布实验功能，再通过 .NET SDK 的预发布版本进行发布。

预览功能可用于目的为针对功能设计和实现收集反馈的测试和试验。 为此，预览功能可能会随着时间的推移以破坏后向兼容性的方式进行更改。 这与 GA 版本中的功能形成鲜明对比，除了小型的后向兼容性修补程序和增强功能外，这些功能很稳定，不太可能发生变化。 此外，预览功能并不总是纳入 GA 版本。

出于这些原因，我们建议不要编写依赖预览版的生产代码。 如果使用的是旧版预览版，建议迁移到正式版 (GA)。

对于 .NET SDK：代码迁移指南可在[升级 .NET SDK](search-dotnet-sdk-migration.md) 中找到。

正式版的推出意味着 Azure 搜索现在遵循服务级别协议 (SLA)。 可以在 [Azure 搜索服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)中找到 SLA。
