---
title: 升级到 Azure 搜索 .NET 管理 SDK
titleSuffix: Azure Cognitive Search
description: 从以前的版本升级到 Azure 搜索 .NET 管理 SDK。 了解执行迁移所需的新功能和代码更改。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936701"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>升级 Azure 搜索 .NET 管理 SDK 的版本

本文介绍了如何迁移到 Azure 搜索 .NET 管理 SDK（用于预配或取消预配搜索服务、调整容量以及管理 API 密钥）的各个连续版本。

管理 SDK 面向管理 REST API 的某个特定版本。 有关概念和操作的详细信息，请参阅[搜索管理 (REST)](/rest/api/searchmanagement/)。

## <a name="versions"></a>版本

| SDK 版本 | 相应的 REST API 版本 | 功能添加或行为变更 |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | api 版本 = 2020-30-20 | 添加端点安全 (IP 防火墙并与 [Azure Private Link](../private-link/private-endpoint-overview.md) 集成)  |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | api-version=2019-10-01 | 可用性改进。 针对[列出查询密钥](/rest/api/searchmanagement/querykeys/listbysearchservice)的中断性变更（GET 已停用）。 |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | api-version=2015-08-19  | 第一个版本 |

## <a name="how-to-upgrade"></a>如何升级

1. 按以下方式更新 `Microsoft.Azure.Management.Search` 的 NuGet 引用：使用 NuGet 包管理器控制台，或者在 Visual Studio 中右键单击项目引用，然后选择“管理 NuGet 程序包...”。

1. 在 NuGet 已下载新的程序包及其依赖项后，请重新生成项目。 根据代码结构的不同，重新生成该项目可能会成功，如果成功则操作完成。

1. 如果生成失败，则可能是因为已实现某些已更改的 SDK 接口（如出于单元测试目）。 若要解决此问题，需要实现 `BeginCreateOrUpdateWithHttpMessagesAsync` 等更新的方法。

1. 在修复了任何生成错误后，可以对应用程序进行更改，以利用新功能。 

## <a name="upgrade-to-30"></a>升级到3。0

版本3.0 通过限制对 IP 范围的访问来添加私有终结点保护，并可选择与在公共 internet 上不可见的搜索服务的 Azure 专用链接集成。

### <a name="new-apis"></a>新 API

| API | 类别| 详细信息 |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | IP 防火墙 | 将对服务终结点的访问限制到允许的 IP 地址的列表。 请参阅 [配置 IP 防火墙](service-configure-firewall.md) 了解概念和门户说明。 |
| [共享的专用链接资源](/rest/api/searchmanagement/sharedprivatelinkresources) | 专用链接 | 创建要由搜索服务使用的共享专用链接资源。  |
| [专用终结点连接](/rest/api/searchmanagement/privateendpointconnections) | 专用链接 | 通过专用终结点建立和管理搜索服务的连接。 请参阅创建概念和门户说明的 [专用终结点](service-create-private-endpoint.md) 。|
| [专用链接资源](/rest/api/searchmanagement/privatelinkresources/) | 专用链接 | 对于具有专用终结点连接的搜索服务，获取同一个虚拟网络中使用的所有服务的列表。 如果搜索解决方案包括从 Azure 数据源提取的索引器 (Azure 存储、Cosmos DB、Azure SQL) ，或者使用认知服务或 Key Vault，则所有这些资源都应在虚拟网络中具有终结点，并且此 API 应返回列表。 |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| 专用链接 | 这是创建或更新服务请求的属性。 禁用时，专用链接是唯一的访问模态。 |

### <a name="breaking-changes"></a>中断性变更

你不能再对 [列出查询密钥](/rest/api/searchmanagement/querykeys/listbysearchservice) 请求使用 GET。 在以前的版本中，你可以使用 GET 或 POST，在此版本中，在所有发布中，只支持 POST。 

## <a name="upgrade-to-20"></a>升级到 2.0

Azure 搜索 .NET 管理 SDK 的版本 2 是一个次要升级，因此更改代码所需要的工作量应该极少。SDK 的更改在严格意义上而言是为改进 SDK 本身可用性而做出的客户端更改。 包括以下更改：

* `Services.CreateOrUpdate` 及其异步版本现在会自动轮询预配 `SearchService`，且在服务预配完成后才返回。 因此，无需自己编写此类轮询代码。

* 如果仍想手动轮询服务预配，可使用新的 `Services.BeginCreateOrUpdate` 方法或其异步版本之一。

* 新方法 `Services.Update` 及其异步版本已添加到 SDK。 这些方法使用 HTTP PATCH 来支持服务的增量更新。 例如，现在可以通过将 `SearchService` 实例传递到仅包含所需 `partitionCount` 和 `replicaCount` 属性的方法来缩放服务。 仍支持调用 `Services.Get`、修改返回的 `SearchService` 并将其传递给 `Services.CreateOrUpdate` 这一旧方法，但该方法不再是必要的。 

## <a name="next-steps"></a>后续步骤

如果遇到问题，最适合发布问题的论坛是 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest)。 如果找到 Bug，可以在 [Azure .NET SDK GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/issues)中提出问题。 请务必使用“[search]”来标记问题标题。