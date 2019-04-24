---
title: Azure Resource Graph 概述
description: 了解如何使用 Azure Resource Graph 服务对资源进行大规模的复杂查询。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/30/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d76a5b32403bd14f18181580f891925130808922
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002878"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Azure Resource Graph 服务概述

Azure Resource Graph 是 Azure 中的一项服务，旨在通过提供高效和高性能的资源浏览来扩展 Azure 资源管理器，它能够跨所有订阅和管理组进行大规模查询，使你能够有效地管理你的环境。 这些查询提供以下功能：

- 通过复杂筛选、分组和按资源属性排序来查询资源的功能。
- 基于管理要求以迭代方式浏览资源并将生成的表达式转换为策略定义的功能。
- 评估在大量云环境中应用策略所产生的影响的功能。
- 能够[详细说明对资源属性所做的更改](./how-to/get-resource-changes.md)（预览版）。

在本文档中，你将逐一了解各项功能的详细信息。

> [!NOTE]
> Azure 门户的全新浏览“所有资源”体验和 Azure 策略的[更改历史记录](../policy/how-to/determine-non-compliance.md#change-history-preview)使用 Azure Resource Graph。
> _视觉差异_。它旨在帮助客户管理大规模环境。

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph 如何补充了 Azure 资源管理器

当前，Azure 资源管理器向有限的资源缓存发送数据，该缓存提供多个资源字段，具体包括资源名称、ID、类型、资源组、订阅和位置。 以前，使用各种资源属性时，必须调用各个资源提供程序，并请求每个资源的属性详细信息。

使用 Azure Resource Graph，可以访问资源提供程序返回的这些属性，无需对资源提供程序进行单独调用。 如需支持的资源类型的列表，请在[完整模式部署的资源](../../azure-resource-manager/complete-mode-deletion.md)表中查找“是”。

使用 Azure Resource Graph，可以：

- 访问资源提供程序返回的属性，而无需对每个资源提供程序进行单独调用。
- 查看过去 14 天对资源所做的更改历史记录，以了解更改了哪些属性以及何时更改。 （预览版）

## <a name="the-query-language"></a>查询语言

现在，你已更好地了解了 Azure Resource Graph，我们来深入了解如何构造查询。

务必要了解的一点是，Azure Resource Graph 的查询语言基于 Azure 数据资源管理器使用的 [Kusto 查询语言](../../data-explorer/data-explorer-overview.md)。

首先，有关可以在 Azure Resource Graph 中使用的操作和函数，请参阅 [Resource Graph 查询语言](./concepts/query-language.md)。
若要浏览资源，请参阅[浏览资源](./concepts/explore-resources.md)。

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph 中的权限

若要使用 Resource Graph，必须在[基于角色的访问控制](../../role-based-access-control/overview.md) (RBAC) 中至少拥有对想要查询的资源的读取权限。 必须至少有 Azure 对象或对象组的 `read` 权限，否则不会返回结果。

> [!NOTE]
> Resource Graph 使用主体在登录期间可用的订阅。 若要查看在活动会话期间添加的新订阅的资源，主体必须刷新上下文。 此操作在注销并重新登录时自动发生。

## <a name="throttling"></a>限制

为了为所有客户提供最佳体验和响应时间，对 Resource Graph 的查询将受到限制。 如果你的组织希望使用 Resource Graph API 进行大规模的频繁查询，请使用 Resource Graph 页面中的门户“反馈”。 请务必提供你的业务案例并选中“Microsoft 可以针对你的反馈向你发送电子邮件”复选框，以便团队与你联系。

## <a name="running-your-first-query"></a>运行自己的第一个查询

Resource Graph 支持 Azure CLI、Azure PowerShell 和用于 .NET 的 Azure SDK。 对于每种语言，查询结构相同。 在 [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module) 中了解如何启用 Resource Graph。

## <a name="next-steps"></a>后续步骤

- 使用 [Azure CLI](first-query-azurecli.md) 运行第一个查询。
- 使用 [Azure PowerShell](first-query-powershell.md) 运行第一个查询。
- 从[初学者查询](./samples/starter.md)开始。
- 增强对[高级查询](./samples/advanced.md)的理解。