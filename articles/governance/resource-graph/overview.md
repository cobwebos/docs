---
title: Azure Resource Graph 概述
description: 了解如何使用 Azure Resource Graph 服务跨订阅和租户对资源进行大规模的复杂查询。
ms.date: 10/14/2020
ms.topic: overview
ms.openlocfilehash: 8e61dadc44a2b07066f7bac761c366c746cef1f1
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057767"
---
# <a name="what-is-azure-resource-graph"></a>什么是 Azure Resource Graph？

Azure Resource Graph 是 Azure 中的一项服务，旨在通过提供高效和高性能的资源浏览来扩展 Azure 资源管理器，它能够跨给定的订阅组进行大规模查询，使你能够有效地管理环境。 这些查询提供以下功能：

- 通过复杂筛选、分组和按资源属性排序来查询资源的功能。
- 基于治理要求以迭代方式浏览资源的功能。
- 评估在大量云环境中应用策略所产生的影响的功能。
- 能够[详细说明对资源属性所做的更改](./how-to/get-resource-changes.md)（预览版）。

在本文档中，你将逐一了解各项功能的详细信息。

> [!NOTE]
> Azure Resource Graph 支持 Azure 门户的搜索栏、全新的浏览“所有资源”体验以及 Azure Policy 的[更改历史记录](../policy/how-to/determine-non-compliance.md#change-history)
> 视觉差异。 它旨在帮助客户管理大规模环境。

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph 如何补充了 Azure 资源管理器

资源管理器目前支持对基本的资源字段进行查询，具体说来，这些字段包括“资源名称”、“ID”、“类型”、“资源组”、“订阅”和“位置”。 资源管理器还提供设施，用于调用各个资源提供程序以获取详细的属性，每次仅限一个资源。

使用 Azure Resource Graph，可以访问资源提供程序返回的这些属性，无需对资源提供程序进行单独调用。 有关支持的资源类型列表，请查阅[表和资源类型参考](./reference/supported-tables-resources.md)。 若要查看支持的资源类型，另一种方法是通过 [Azure Resource Graph 资源管理器架构浏览器](./first-query-portal.md#schema-browser)。

使用 Azure Resource Graph，可以：

- 访问资源提供程序返回的属性，而无需对每个资源提供程序进行单独调用。
- 查看过去 14 天对资源所做的更改历史记录，以了解更改了哪些属性以及何时更改。 （预览版）

> [!NOTE]
> 作为预览功能，一些 `type` 对象可提供其他非资源管理器属性。 有关详细信息，请参阅[扩展属性（预览版）](./concepts/query-language.md#extended-properties)。

## <a name="how-resource-graph-is-kept-current"></a>如何让 Resource Graph 保持最新

更新 Azure 资源时，资源管理器会将所做的更改通知给 Resource Graph。
Resource Graph 然后就会更新其数据库。 Resource Graph 也会定期进行完全扫描。 此扫描可确保在缺少通知时，或者当资源是在资源管理器外部进行更新时，Resource Graph 数据能够保持最新。

> [!NOTE]
> Resource Graph 使用每个资源提供程序的最新非预览版 API 的 `GET` 来收集属性和值。 因此，预期的属性可能不可用。 在某些情况下，会覆盖所使用的 API 版本，以便在结果中提供更多当前或广泛使用的属性。 有关环境中的完整列表，请参阅[显示每种资源类型的 API 版本](./samples/advanced.md#apiversion)示例。

## <a name="the-query-language"></a>查询语言

现在，你已更好地了解了 Azure Resource Graph，我们来深入了解如何构造查询。

务必要了解的一点是，Azure Resource Graph 的查询语言基于 Azure 数据资源管理器使用的 [Kusto 查询语言](/azure/data-explorer/data-explorer-overview)。

首先，有关可以在 Azure Resource Graph 中使用的操作和函数，请参阅 [Resource Graph 查询语言](./concepts/query-language.md)。 若要浏览资源，请参阅[浏览资源](./concepts/explore-resources.md)。

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph 中的权限

若要使用 Resource Graph，必须在 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 中拥有适当的权限，以便对想要查询的资源至少进行读取访问。 必须至少有 Azure 对象或对象组的 `read` 权限，否则不会返回结果。

> [!NOTE]
> Resource Graph 使用主体在登录期间可用的订阅。 若要查看在活动会话期间添加的新订阅的资源，主体必须刷新上下文。 此操作在注销并重新登录时自动发生。

Azure CLI 和 Azure PowerShell 使用用户有权访问的订阅。 直接使用 REST API 时，订阅列表由用户提供。 如果用户有权访问列表中的任何订阅，则返回用户有权访问的订阅的查询结果。 此行为与调用 [Resource Groups - List](/rest/api/resources/resourcegroups/list) \- 时相同，你可以获得有权访问的资源组，而不会指示结果可能是部分的。 如果订阅列表中没有用户具有适当权限的订阅，则响应为“403 (已禁止)”。

> [!NOTE]
> 在 REST API 预览版 `2020-04-01-preview` 中，可能会省略订阅列表。
> 如果未在请求中定义 `subscriptions` 和 `managementGroupId` 属性，范围将设置为“租户”。 有关详细信息，请参阅[查询范围](./concepts/query-language.md#query-scope)。

## <a name="throttling"></a>限制

为了为所有客户提供最佳体验和响应时间，对 Resource Graph 的查询（以免费服务的形式提供）将受到限制。 如果你的组织希望使用 Resource Graph API 进行大规模的频繁查询，请使用 [Resource Graph 门户页面](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph)中的门户“反馈”。
请提供你的业务案例并选中“Microsoft 可以针对你的反馈向你发送电子邮件”复选框，以便团队与你联系。

Resource Graph 在用户级别对查询进行限制。 服务响应包含以下 HTTP 标头：

- `x-ms-user-quota-remaining` (int)：用户的剩余资源配额。 此值映射到查询计数。
- `x-ms-user-quota-resets-after` (hh:mm:ss)：在用户的配额消耗量重置之前的持续时间

有关详细信息，请参阅[针对受限制请求的指南](./concepts/guidance-for-throttled-requests.md)。

## <a name="running-your-first-query"></a>运行自己的第一个查询

Azure Resource Graph 资源管理器是 Azure门户的一部分，支持直接在 Azure 门户中运行 Resource Graph 查询。 将结果固定为动态图表，以便向门户工作流提供实时动态信息。 有关详细信息，请参阅[使用 Azure Resource Graph 资源管理器进行第一次查询](./first-query-portal.md)。

Resource Graph 支持 Azure CLI、Azure PowerShell、用于 Python 的 Azure SDK 等。 对于每种语言，查询结构相同。 了解如何使用以下项启用 Resource Graph：

- [Azure 门户和 Resource Graph 资源管理器](./first-query-portal.md) 
- [Azure CLI](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>后续步骤

- 详细了解[查询语言](./concepts/query-language.md)。
- 请参阅[初学者查询](./samples/starter.md)中使用的语言。
- 请参阅[高级查询](./samples/advanced.md)中的高级用法。
