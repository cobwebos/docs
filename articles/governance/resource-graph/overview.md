---
title: Azure Resource Graph 概述
description: Azure Resource Graph 是一项 Azure 服务，可实现对资源的大规模复杂查询。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d68183f4d0a928ac72f3f73ea5225ad174820cb7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162094"
---
# <a name="what-is-azure-resource-graph"></a>什么是 Azure Resource Graph

Azure Resource Graph 是 Azure 中的一项服务，旨在通过提供高效和高性能的资源浏览来扩展 Azure 资源管理器，它能够跨所有订阅和管理组进行大规模查询，使你能够有效地管理你的环境。 这些查询提供以下功能：

- 通过复杂筛选、分组和按资源属性排序来查询资源的功能。
- 基于管理要求以迭代方式浏览资源并将生成的表达式转换为策略定义的功能。
- 评估在大量云环境中应用策略所产生的影响的功能。

在本文档中，你将逐一了解各项功能的详细信息。

> [!NOTE]
> Azure Resource Graph 被 Azure 门户用于提供全新的浏览“所有资源”的体验。 它旨在帮助需要管理大规模环境的客户。

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph 如何补充了 Azure 资源管理器

当前，Azure Resource Manager 向有限的资源缓存发送数据，该缓存公开多个资源字段，具体包括资源名称、ID、类型、资源组、订阅和位置。 现在，如果你想要使用更多资源属性，必须调用单独的资源提供程序，并请求每个资源的属性详细信息。

使用 Azure Resource Graph，可以访问资源提供程序返回的这些属性，无需对资源提供程序进行单独调用。

## <a name="the-query-language"></a>查询语言

现在，你已更好地了解了 Azure Resource Graph，我们来深入了解如何构造查询。

务必要了解的一点是，Azure Resource Graph 的查询语言基于 [Azure 数据资源管理器查询语言](../../data-explorer/data-explorer-overview.md)。

首先，有关可以在 Azure Resource Graph 中使用的操作和函数，请参阅 [Resource Graph 查询语言](./concepts/query-language.md)。 若要浏览资源，请参阅[浏览资源](./concepts/explore-resources.md)。

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph 中的权限

若要使用 Resource Graph，必须通过[基于角色的访问控制](../../role-based-access-control/overview.md) (RBAC) 至少授予对想要查询的资源的读取权限。 如果你没有管理组、订阅、资源组或单个资源的`read`权限，则 Resource Graph 查询不会返回相关结果。

## <a name="running-your-first-query"></a>运行自己的第一个查询

Resource Graph 支持 Azure CLI 和 Azure PowerShell。 无论使用哪种语言，查询组件的构造方式是一样的。 SDK 中尚未提供默认的 Azure Resource Graph 的支持，所以必须加载扩展或模块，以提供所需的命令。
在 [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module) 中了解如何启用 Resource Graph。

## <a name="next-steps"></a>后续步骤

- 在 [Azure CLI](first-query-azurecli.md) 中运行首个查询
- 在 [Azure PowerShell](first-query-powershell.md) 中运行你的第一个查询。
- 开始使用[初学者查询](./samples/starter.md)
- 增强对[高级查询](./samples/advanced.md)的理解。