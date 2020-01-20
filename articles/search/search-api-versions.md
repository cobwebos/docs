---
title: 适用于 .NET 和 REST 的 API 版本管理
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索 REST Api 和 .NET SDK 中的客户端库的版本策略。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c2f774401f32ff02ced690173c418dafc4c789b5
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278810"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure 认知搜索中的 API 版本

Azure 认知搜索会定期推出功能更新。 这些更新有时（但并非总是）需要发布 API 的新版本，以保持后向兼容性。 发布新版本使你可以控制何时以及如何在代码中集成搜索服务更新。

作为一种规则，Azure 认知搜索团队仅在必要时才发布新版本，因为这可能需要将代码升级为使用新的 API 版本。 仅在 API 的某些方面以破坏后向兼容性的方式发生更改时，才需要新版本。 此类更改可能会因以下情况而发生：现有功能修复，或更改现有 API 外围功能的新功能。

相同规则适用于 SDK 更新。 Azure 认知搜索 SDK 遵循[语义版本控制](https://semver.org/)规则，这意味着其版本包含三个部分：主要版本号、次要版本号和内部版本号（例如，1.1.0）。 仅在更改破坏了后向兼容性时，才会发布新的 SDK 主版本。 非破坏功能更新会增大次要版本号，Bug 修复仅增大内部版本号。

> [!NOTE]
> Azure 认知搜索服务实例支持多个 REST API 版本，包括最新版本。 可以不使用最新版本，但是我们建议迁移代码，以便使用最新版本。 使用 REST API 时，必须在每个请求中通过 api-version 参数指定 API 版本。 使用 .NET SDK 时，使用的 SDK 版本确定对应的 REST API 版本。 如果使用较旧的 SDK，即使已升级服务以支持较新的 API 版本，也可以继续运行代码，而不进行任何更改。

## <a name="snapshot-of-current-versions"></a>当前版本的快照
下面是 Azure 认知搜索的所有编程接口的当前版本的快照。


| 界面 | 最新的主要版本 | 状态 |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |公开发布，可能为2019 |
| [.NET SDK 预览版](https://aka.ms/search-sdk-preview) |8.0-预览 |预览版，2019年4月发布 |
| [服务 REST API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |正式版 |
| [Service REST API 2019-05-06-预览版](search-api-preview.md) |2019-05-06-Preview |预览 |
| [.NET 管理 SDK](https://aka.ms/search-mgmt-sdk) |3.0 |正式版 |
| [管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |正式版 |

对于 REST API，每次调用都必须包括 `api-version`。 使用 `api-version` 使适配特定版本（如预览版 API）变得非常容易。 以下示例演示了如何指定 `api-version` 参数：

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> 尽管每个请求都带有 `api-version`，我们还是建议对所有 API 请求使用相同版本。 当新的 API 版本引入了以前版本无法识别的属性或操作时，尤其如此。 混用 API 版本可能产生意想不到的后果，应该避免混用。
>
> 服务 REST API 和管理 REST API 的版本控制彼此独立。 版本号如有相似之处，实属巧合。

正式版（或 GA）API 可以在生产中使用，并遵循 Azure 服务级别协议。 预览版中的实验功能并不总是会迁移到 GA 版本。 强烈建议不要在生产应用程序中使用预览版 API。

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>2020年10月15日更新到最新版本的 REST API
Azure 认知搜索 REST API 的以下版本将会停用，不再受支持，从2020年10月15日： **2014-07-31-preview**、 **2014-10-20-preview**、 **2015-02-28**和**2015-02-28**。 此外，还将停用早于**3.0.0**的 Azure 认知搜索 .net SDK 的版本，因为这些版本以这些 REST API 版本之一为目标。 在此日期之后，使用任何弃用的 REST API 或 SDK 版本的应用程序将不再有效，必须进行升级。 对于这种类型的任何更改，我们将提供12个月的通知，以便你有足够的时间进行调整。  若要继续使用 Azure 认知搜索，请将以[REST API](search-api-migration.md)为目标的现有代码迁移[REST API 到2019-05-06 版](https://docs.microsoft.com/rest/api/searchservice/)或更高版本的或更高版本[3.0](search-dotnet-sdk-migration.md)或更高版本（2020）。  如果你有关于更新到最新版本的任何问题，请发送邮件到 azuresearch_contact@microsoft.com 5 月15日2020，以确保有足够的时间来更新你的代码。

## <a name="about-preview-and-generally-available-versions"></a>关于预览版和正式版
Azure 认知搜索始终先通过 REST API 预发布实验功能，然后通过 .NET SDK 的预发布版本预先发布。

预览功能可用于目的为针对功能设计和实现收集反馈的测试和试验。 为此，预览功能可能会随着时间的推移以破坏后向兼容性的方式进行更改。 这与 GA 版本中的功能形成鲜明对比，除了小型的后向兼容性修补程序和增强功能外，这些功能很稳定，不太可能发生变化。 此外，预览功能并不总是纳入 GA 版本。

出于这些原因，我们建议不要编写依赖预览版的生产代码。 如果使用的是旧版预览版，建议迁移到正式版 (GA)。

对于 .NET SDK：代码迁移指南可在[升级 .NET SDK](search-dotnet-sdk-migration-version-9.md) 中找到。

公开上市意味着 Azure 认知搜索现在低于服务级别协议（SLA）。 可以在[Azure 认知搜索服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)中找到 SLA。
