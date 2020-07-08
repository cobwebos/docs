---
title: API 版本
titleSuffix: Azure Cognitive Search
description: 适用于 .NET SDK 中的 Azure 认知搜索 REST API 和客户端库的版本策略。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830088"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure 认知搜索中的 API 版本

Azure 认知搜索会定期发布功能更新。 这些更新有时（但并非总是）需要发布 API 的新版本，以保持后向兼容性。 发布新版本使你可以控制何时以及如何在代码中集成搜索服务更新。

通常，Azure 认知搜索团队仅在必要时发布新版本，因为它可能会促使你必须升级代码才能使用新版本 API。 仅在 API 的某些方面以破坏后向兼容性的方式发生更改时，才需要新版本。 此类更改可能会因以下情况而发生：现有功能修复，或更改现有 API 外围功能的新功能。

相同规则适用于 SDK 更新。 Azure 认知搜索 SDK 遵循[语义版本控制](https://semver.org/)规则，这意味着其版本分为三个部分：主要版本号、次要版本号和内部版本号（例如，1.1.0）。 仅在更改破坏了后向兼容性时，才会发布新的 SDK 主版本。 非破坏功能更新会增大次要版本号，Bug 修复仅增大内部版本号。

> [!NOTE]
> Azure 认知搜索服务实例支持多个 REST API 版本，包括最新版本。 可以不使用最新版本，但是我们建议迁移代码，以便使用最新版本。 使用 REST API 时，必须在每个请求中通过 api-version 参数指定 API 版本。 使用 .NET SDK 时，使用的 SDK 版本确定对应的 REST API 版本。 如果使用较旧的 SDK，即使已升级服务以支持较新的 API 版本，也可以继续运行代码，而不进行任何更改。

## <a name="rest-apis"></a>REST API

此表提供当前和以前发布的搜索服务 REST API 版本历史记录。 为当前稳定版本和预览版本发布文档。

| 版本&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 状态 | 向后兼容性问题 |
|-------------|--------|------------------------------|
| [管理 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | 正式版 | 最新稳定版本的管理 REST Api，并在 endpoint protection 方面做了改进。 为新服务添加专用终结点、专用链接支持和网络规则。 |
| [管理 2019-10-01-Preview](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | 预览  | 尽管它是版本号，但这仍然是管理 REST Api 的当前预览版本。 目前没有任何预览功能。 所有预览功能最近已转换为公开上市。 |
| 管理2015-08-19  | Stable| 管理 REST Api 的第一个公开发布的版本。 提供服务设置、扩展和 api 密钥管理。 |
| 管理 2015-08-19-预览 | 预览| 管理 REST Api 的第一个预览版本。 |
| [搜索 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Stable | 搜索搜索 REST Api 的最新稳定版本，相关评分的进步。 |
| [搜索 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview)| 预览 | 与稳定版本关联的预览版本。 |
| 搜索 2019-05-06 | Stable | 添加复杂类型。 |
| 搜索 2019-05-06-Preview | 预览 | 与稳定版本关联的预览版本。 |
| 搜索2017-11-11 | Stable  | 添加技能集和 AI 扩充。 |
| 搜索 2017-11-11-预览 | 预览 | 与稳定版本关联的预览版本。 |
| 搜索2016-09-01 |Stable | 添加索引器|
| 搜索 2016-09-01-预览 | 预览 | 与稳定版本关联的预览版本。|
| 搜索2015-02-28 | Stable  | 第一个公开发布的版本。  |
| 搜索 2015-02-28-预览 | 预览 | 与稳定版本关联的预览版本。 |
| 搜索 2014-10-20-预览 | 预览 | 第二公共预览。 |
| 搜索 2014-07-31-预览 | 预览 | 首个公共预览版。 |

## <a name="azure-sdk-for-net"></a>用于 .NET 的 Azure SDK

NuGet.org 上提供了包版本历史记录。此表提供了每个包页的链接。

| SDK 版本 | 状态 | 描述 |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0-预览版4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | 预览 | Azure .NET SDK 中的新客户端库（已发布）可能为2020。 面向 REST 2020-06-30 API 版本|
| [**Microsoft Azure. 搜索10。0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | 公开发布，可能为2019。 面向 REST 2019-05-06 API 版本。|
| [**Microsoft Azure. 搜索 8.0-preview**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | 预览版，2019年4月发布。 面向 REST 2019-05-06-Preview API 版本。|
| [**3.0.0 的搜索**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | 面向管理 REST api 版本 = 2015-08-19。 |

## <a name="azure-sdk-for-java"></a>用于 Java 的 Azure SDK

| SDK 版本 | 状态 | 描述  |
|-------------|--------|------------------------------|
| [**Java SearchManagementClient 1.35。0**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | 面向管理 REST api 版本 = 2015-08-19。|

## <a name="azure-sdk-for-python"></a>用于 Python 的 Azure SDK

| SDK 版本 | 状态 | 描述  |
|-------------|--------|------------------------------|
| [**Python azure 管理-搜索1。0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Stable | 面向管理 REST api 版本 = 2015-08-19。 |