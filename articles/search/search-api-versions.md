---
title: API 版本
titleSuffix: Azure Cognitive Search
description: 适用于 .NET SDK 中的 Azure 认知搜索 REST API 和客户端库的版本策略。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: bf3e4262f4342788f343ab287fd3db53d12736c7
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918105"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure 认知搜索中的 API 版本

Azure 认知搜索会定期发布功能更新。 这些更新有时（但并非总是）需要发布 API 的新版本，以保持后向兼容性。 发布新版本使你可以控制何时以及如何在代码中集成搜索服务更新。

通常，Azure 认知搜索团队仅在必要时发布新版本，因为它可能会促使你必须升级代码才能使用新版本 API。 仅在 API 的某些方面以破坏后向兼容性的方式发生更改时，才需要新版本。 此类更改可能会因以下情况而发生：现有功能修复，或更改现有 API 外围功能的新功能。

相同规则适用于 SDK 更新。 Azure 认知搜索 SDK 遵循[语义版本控制](https://semver.org/)规则，这意味着其版本分为三个部分：主要版本号、次要版本号和内部版本号（例如，1.1.0）。 仅在更改破坏了后向兼容性时，才会发布新的 SDK 主版本。 非破坏功能更新会增大次要版本号，Bug 修复仅增大内部版本号。

> [!Important]
> 适用于 .NET 的 Azure Sdk、Java、Python 和 JavaScript 正在推出 Azure 认知搜索的新客户端库。 目前，没有任何 Azure SDK 库完全支持最新的搜索 REST Api (2020-06-30) 或管理 REST Api (2020-03-13) 但这会随时间而改变。 可以定期查看此页面或有关功能增强的公告的 [新增](whats-new.md) 功能。 

## <a name="rest-apis"></a>REST API

Azure 认知搜索服务实例支持几个 REST API 版本，包括最新版本。 如果某个版本不再是最新版本，则可以继续使用该版本，但我们建议你 [迁移代码](search-api-migration.md) 以使用最新版本。 使用 REST API 时，必须在每个请求中通过 api-version 参数指定 API 版本。 使用 .NET SDK 时，使用的 SDK 版本确定对应的 REST API 版本。 如果使用较旧的 SDK，即使已升级服务以支持较新的 API 版本，也可以继续运行代码，而不进行任何更改。

下表提供了搜索服务 REST API 的当前版本和以前发布版本的版本历史记录。 文档仅适用于最新的稳定和预览版本。

### <a name="search-service-apis"></a>搜索服务 Api

在搜索服务中创建和管理内容。

| 版本&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 状态 | 向后兼容性问题 |
|-------------------------|--------|------------------------------|
| [搜索 2020-06-30](/rest/api/searchservice/index)| Stable | 搜索 REST Api 的最新稳定版本，在相关性评分和一般可用性方面进行了改进。|
| [搜索 2020-06-30-Preview](/rest/api/searchservice/index-preview)| 预览 | 与稳定版本关联的预览版本。 包括多个 [预览功能](search-api-preview.md)。 |
| 搜索 2019-05-06 | Stable | 添加 [复杂类型](search-howto-complex-data-types.md)。 |
| 搜索 2019-05-06-Preview | 预览 | 与稳定版本关联的预览版本。 |
| 搜索 2017-11-11 | Stable  | 添加技能集和 [AI 扩充](cognitive-search-concept-intro.md)。 |
| 搜索 2017-11-11-Preview | 预览 | 与稳定版本关联的预览版本。 |
| 搜索 2016-09-01 |Stable | 添加 [索引器](search-indexer-overview.md)。 |
| 搜索 2016-09-01-Preview | 预览 | 与稳定版本关联的预览版本。|
| 搜索 2015-02-28 | Stable  | 第一个正式发布版本。  |
| 搜索 2015-02-28-Preview | 预览 | 与稳定版本关联的预览版本。 |
| 搜索 2014-10-20-Preview | 预览 | 第二个公共预览版。 |
| 搜索 2014-07-31-Preview | 预览 | 第一个公共预览版。 |

### <a name="management-rest-apis"></a>管理 REST API

创建和配置搜索服务，并管理 API 密钥。

| 版本&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 状态 | 向后兼容性问题 |
|-------------------------|--------|------------------------------|
| [管理 2020-03-13](/rest/api/searchmanagement/) | Stable | 管理 REST API 的最新稳定版本，在终结点保护方面有改进。 通过专用链接、新服务的[网络 IP 规则](service-configure-firewall.md)添加[专用终结点](service-create-private-endpoint.md)。 |
| [管理 2019-10-01-Preview](/rest/api/searchmanagement/index-2019-10-01-preview) | 预览  | 尽管具有版本号，但它仍是管理 REST API 的当前预览版。 目前没有预览功能。 所有预览功能最近已转换为正式发布版本。 |
| 管理 2015-08-19  | Stable | 管理 REST API 的第一个正式发布版本。 提供服务预配、纵向扩展和 API 密钥管理。 |
| 管理 2015-08-19-Preview | 预览 | 管理 REST API 的第一个预览版本。 |

## <a name="azure-sdk-for-net"></a>用于 .NET 的 Azure SDK

NuGet.org 上提供了包版本历史记录。此表提供了每个包页的链接。


| SDK 版本 | 状态 | 说明 |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11。0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Stable | 从2020年7月发布的 Azure .NET SDK 中的新客户端库。 面向搜索 REST api 版本 = 2020-06-30 REST API 但尚不支持、异地筛选器或 [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet)。 |
| [Microsoft.Azure.Search 10.0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | 发布时间可能为2019。 面向搜索 REST api 版本 = 2019-05-06。|
| [Microsoft.Azure.Search 8.0-preview](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | 预览 | 2019年4月发布。 面向搜索 REST api 版本 = 2019-05-06-01.5.1-Preview。|
| [Microsoft.Azure.Management.Search 3.0.0](/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | 针对管理 REST api-version=2015-08-19。  |

## <a name="azure-sdk-for-java"></a>用于 Java 的 Azure SDK

| SDK 版本 | 状态 | 说明  |
|-------------|--------|------------------------------|
| [Java azure-搜索-文档11](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | Stable | 从2020年7月发布的 Azure .NET SDK 中的新客户端库。 面向搜索 REST api 版本 = 2019-05-06。 |
| [Java 管理客户端1.35。0](/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | 针对管理 REST api-version=2015-08-19。 |

## <a name="azure-sdk-for-javascript"></a>用于 JavaScript 的 Azure SDK

| SDK 版本 | 状态 | 说明  |
|-------------|--------|------------------------------|
| [JavaScript azure-搜索11。0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | 从2020年7月发布的 Azure .NET SDK 中的新客户端库。 面向搜索 REST api 版本 = 2016-09-01。 |
| [JavaScript-搜索](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | 针对管理 REST api-version=2015-08-19。 |

## <a name="azure-sdk-for-python"></a>用于 Python 的 Azure SDK

| SDK 版本 | 状态 | 说明  |
|-------------|--------|------------------------------|
| [Python azure-搜索-文档11。0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | 从2020年7月发布的 Azure .NET SDK 中的新客户端库。 面向搜索 REST api 版本 = 2019-05-06。 |
| [Python azure-mgmt-search 1.0](/python/api/overview/azure/search?view=azure-python) | Stable | 针对管理 REST api-version=2015-08-19。 |