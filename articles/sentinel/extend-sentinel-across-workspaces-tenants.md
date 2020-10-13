---
title: 跨工作区和租户扩展 Azure Sentinel |Microsoft Docs
description: 如何使用 Azure Sentinel 跨工作区和租户查询和分析数据。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: 502b93b4459fba4da04207d9186f8c7ce6b298c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578472"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>跨工作区和租户扩展 Azure Sentinel

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>需要使用多个 Azure Sentinel 工作区

Azure Sentinel 构建在 Log Analytics 工作区之上。 你会注意到，加入 Azure Sentinel 的第一步是选择要用于此目的的 Log Analytics 工作区。

使用单个工作区时，你可以充分利用 Azure Sentinel 体验。 尽管如此，在某些情况下，可能需要使用多个工作区。 下表列出了其中的一些情况，并在可能的情况下指出如何对单个工作区满足要求：

| 要求 | 说明 | 减少工作区计数的方法 |
|-------------|-------------|--------------------------------|
| 主权和法规遵从性 | 工作区绑定到特定区域。 如果需要将数据保留在不同的 [Azure 区域](https://azure.microsoft.com/global-infrastructure/geographies/) 以满足法规要求，则必须将其拆分为单独的工作区。 |  |
| 数据所有权 | 例如，通过使用单独的工作区，可以更好地区分数据所有权边界（例如，由子公司或附属公司）。 |  |
| 多个 Azure 租户 | Azure Sentinel 支持仅在其自己的 Azure Active Directory (Azure AD) 租户边界内收集来自 Microsoft 和 Azure SaaS 资源的数据。 因此，每个 Azure AD 租户都需要一个单独的工作区。 |  |
| 精细数据访问控制 | 组织可能需要允许组织内部或外部的不同组访问 Azure Sentinel 收集的某些数据。 例如：<br><ul><li>资源所有者对与其资源相关的数据的访问权限</li><li>地区性或子公司 Soc 访问其组织的各个部分的相关数据</li></ul> | 使用 [资源 rbac](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) 或 [表级别 rbac](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| 精细保留设置 | 过去，有多个工作区是为不同的数据类型设置不同的保持期的唯一方法。 在许多情况下，这在很多情况下都不再需要，这是因为引入了表级别的保留设置。 | 使用 [表级别的保留设置](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) 或自动 [删除数据](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| 拆分计费 | 通过将工作区放在不同的订阅中，可以对不同的参与方计费。 | 使用情况报告和交叉收费 |
| 旧体系结构 | 使用多个工作区可能源自一个历史设计，该设计考虑到了不会再用到的限制或最佳做法。 它也可能是一个任意的设计方案，经过修改后可以更好地适应 Azure Sentinel。<br><br>示例包括：<br><ul><li>部署 Azure 安全中心时使用每个订阅默认工作区</li><li>需要精细的访问控制或保留设置，这是相对较新的解决方案</li></ul> | 重新构建工作区 |

### <a name="managed-security-service-provider-mssp"></a>托管安全服务提供商 (MSSP) 

要求多个工作区的特定用例是 MSSP Azure Sentinel 服务。 在这种情况下，很多情况下，如果不满足上述所有要求，则在租户中创建多个工作区，这是最佳做法。 MSSP 可以使用 [Azure Lighthouse](../lighthouse/overview.md) 跨租户扩展 azure Sentinel 跨工作区功能。

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Azure Sentinel 多个工作区体系结构

正如上面的要求所隐含的那样，在某些情况下，需要通过单个 SOC 来集中监视和管理多个 Azure Sentinel 工作区（可能跨 Azure Active Directory (Azure AD) 租户）。

- MSSP Azure Sentinel 服务。

- 全局 SOC 为多个子公司提供服务，每个子公司都有自己的本地 SOC。

- SOC 监视组织中的多个 Azure AD 租户。

为了满足这一要求，Azure Sentinel 提供了多个工作区功能，可实现集中监视、配置和管理，并跨 SOC 涵盖的所有内容提供单个窗格，如下图所示。

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="跨工作区体系结构":::

此模型的优点优于完全集中式模型，在该模型中，所有数据都将复制到单个工作区：

- 灵活地将角色分配给全局和本地 Soc，或 MSSP 其客户。

- 对数据所有权、数据隐私和法规遵从性的挑战更少。

- 网络延迟最短且收费最便宜。

- 轻松加入和脱离新的子公司或客户。

在以下部分中，我们将介绍如何操作此模型，以及如何执行以下操作：

- 集中监视多个工作区（可能跨租户），并通过单一的玻璃窗格提供 SOC。

- 使用自动化跨租户集中配置和管理多个工作区。

## <a name="cross-workspace-monitoring"></a>跨工作区监视

### <a name="manage-incidents-on-multiple-workspaces"></a>管理多个工作区上的事件

Azure Sentinel 支持 [多个工作区事件，查看](./multiple-workspace-view.md) 跨多个工作区进行中心事件监视和管理。 利用集中事件视图，你可以直接管理事件或在原始工作区的上下文中以透明方式向下钻取到事件详细信息。

### <a name="cross-workspace-querying"></a>跨工作区查询

Azure Sentinel 支持 [在单个查询中查询多个工作区](../azure-monitor/log-query/cross-workspace-query.md)，以便在单个查询中搜索和关联来自多个工作区的数据。 

- 使用 [工作区 ( # A1 表达式](../azure-monitor/log-query/workspace-expression.md) 引用其他工作区中的表。 
- 使用工作区和工作区 ( # A1 [表达式，在](https://docs.microsoft.com/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) 多个工作区中的表之间应用查询。

您可以使用保存的 [函数](../azure-monitor/log-query/functions.md) 来简化跨工作区查询。 例如，如果对工作区的引用很长，则可能需要将表达式保存 `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` 为名为的函数 `SecurityEventCustomerA` 。 然后，你可以将查询编写为 `SecurityEventCustomerA | where ...` 。

函数还可以简化常用的联合。 例如，可以将以下表达式保存为名为的函数 `unionSecurityEvent` ：

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

然后，你可以通过从开始，在这两个工作区中编写查询 `unionSecurityEvent | where ...` 。

#### <a name="scheduled-alerts"></a>计划的警报

跨工作区查询现在可以包含在分析规则的计划警报中，但有以下限制：

- 单个查询最多可包含10个工作区。
- Azure Sentinel 必须部署在查询中引用的每个工作区上。

> [!NOTE] 
> 在同一查询中查询多个工作区可能会影响性能，因此仅在逻辑需要此功能时才建议使用。

### <a name="using-cross-workspace-workbooks"></a>使用跨工作区工作簿

[工作簿](./overview.md#workbooks) 将仪表板和应用提供给 Azure Sentinel。 当使用多个工作区时，它们提供跨工作区的监视和操作。

工作簿可采用以下三种方法之一提供跨工作区查询，每种方法都可满足不同级别的最终用户专长：

| 方法  | 说明 | 何时应使用？ |
|---------|-------------|--------------------|
| 编写跨工作区查询 | 工作簿创建者可以 (以上) 在工作簿中的说明编写跨工作区查询。 | 此选项使工作簿创建者能够完全从工作区结构中保护用户。 |
| 向工作簿添加工作区选择器 | 工作簿的创建者可以实现工作区选择器作为工作簿的一部分，如 [此处](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357)所述。 | 使用此选项，用户可以通过易于使用的下拉框来控制工作簿显示的工作区。 |
| 以交互方式编辑工作簿 | 修改现有工作簿的高级用户可以在其中编辑查询，使用编辑器中的工作区选择器选择目标工作区。 | 此选项使 Power User 可以轻松修改现有工作簿，以便使用多个工作区。 |
|

### <a name="cross-workspace-hunting"></a>跨工作区搜寻

Azure Sentinel 提供预先加载的查询示例，旨在帮助您入门并熟悉表和查询语言。 这些内置的搜索查询是由 Microsoft 安全研究人员不断地开发的，其中包括添加新查询和微调现有查询，为你提供一个用于查找新检测并识别可能已被安全工具检测到的入侵迹象的入口点。  

通过使用 union 运算符和工作区 ( # A1 表达式，你可以通过跨工作区搜寻功能创建新的搜寻查询，或改编现有的搜寻查询，以涵盖多个工作区。

## <a name="cross-workspace-management-using-automation"></a>使用自动化的跨工作区管理

若要配置和管理多个 Azure Sentinel 工作区，需要自动使用 Azure Sentinel 管理 API。 有关如何自动部署 Azure Sentinel 资源的详细信息，包括警报规则、搜寻查询、工作簿和行动手册，请参阅 [扩展 Azure sentinel： api、集成和管理自动化](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885)。

另请参阅 [部署和管理 Azure Sentinel 作为代码](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) ，以及如何将 azure [Lighthouse 与 Sentinel 的 DevOps 功能结合使用](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) ，以获得用于将 azure sentinel 作为代码进行管理并从专用 GitHub 存储库部署和配置资源的合并社区贡献式方法。 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>使用 Azure Lighthouse 跨租户管理工作区

如上所述，在许多情况下，不同的 Azure Sentinel 工作区可以位于不同的 Azure AD 租户中。 可以使用 [Azure Lighthouse](../lighthouse/overview.md) 跨租户边界扩展所有跨工作区活动，并允许管理租户中的用户在所有租户中使用 Azure Sentinel 工作区。 一旦 Azure Lighthouse 为 [载入](../lighthouse/how-to/onboard-customer.md)，请使用 Azure 门户上的 " [目录 + 订阅](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants) " 选择器来选择包含要管理的工作区的所有订阅，以确保它们将在门户的不同工作区选择器中可用。

使用 Azure Lighthouse 时，建议为每个 Azure Sentinel 角色创建一个组，并将每个租户的权限委托给这些组。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何跨多个工作区和租户扩展 Azure Sentinel 的功能。 有关实现 Azure Sentinel 跨工作区体系结构的实用指导，请参阅以下文章：

- 了解如何使用 Azure Lighthouse 在 Azure Sentinel 中 [处理多个租户](./multiple-tenants-service-providers.md) 。
- 了解如何无缝 [查看和管理多个工作区中的事件](./multiple-workspace-view.md) 。