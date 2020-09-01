---
title: Azure 资源关系图 Azure 安全性基准
description: Azure 资源关系图安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b67397db3cc6de2a0e6658795ddf87ad355ad422
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078361"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Azure 资源关系图 Azure 安全性基准

此安全基线将 [Azure 安全性基准](../../../security/benchmarks/overview.md) 中的指南应用于 Azure 资源关系图。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准定义的 **安全控件** 进行分组，以及适用于 Azure 资源关系图的相关指南。 排除了不适用于 Azure 资源关系图的**控件**。 若要查看 Azure 资源图如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure 虚拟网络安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。



## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../../../security/benchmarks/security-control-identity-access-control.md)。

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**： Azure 资源图提供了基于 azure 基于角色的访问控制 (azure RBAC) 的资源类型和属性的访问权限。 定期审核并查看授予安全主体的访问权限 (用户、组和服务帐户定期) ，确保查询返回相应资源的结果。

* [Azure Resource Graph 中的权限](../overview.md#permissions-in-azure-resource-graph)

* [如何使用 Azure 标识访问评审](../../../active-directory/governance/access-reviews-overview.md)


**Azure 安全中心监视**：是

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../../../security/benchmarks/security-control-data-protection.md)。

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用 azure RBAC)  (azure 基于角色的访问控制来控制对数据和资源的访问。 若要使用 Azure 资源关系图，还必须对要查询的资源具有适当的访问权限。 此访问权限的范围应为只读，并且仅授予所需人员。

* [Azure Resource Graph 中的权限](../overview.md#permissions-in-azure-resource-graph)

* [如何配置 Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)


**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现订阅、管理组和租户中所有受支持的资源。 确保你在租户中拥有适当的权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

* [如何使用 Azure Resource Graph 创建查询](../first-query-portal.md)

* [了解 Azure RBAC](../../../role-based-access-control/overview.md)


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指南**：根据组织需求为计算资源创建已批准的 Azure 资源和批准的软件的清单。 使用 Azure 资源关系图查询已批准的 Azure 资源并更改历史记录 (预览) 以查看快照并查看更改的内容。

* [用 Azure 资源图查询 Azure 资源](../first-query-portal.md)

* [获取资源更改](../how-to/get-resource-changes.md)


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure 资源关系图可查询和发现订阅、管理组和租户中的资源。 请确保环境中的所有 Azure 资源都已获得批准。

* [用 Azure 资源图查询 Azure 资源](../first-query-portal.md)

* [示例： Azure 资源关系图的 Starter 查询](../samples/starter.md)


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../../../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../../../security/benchmarks/security-baselines-overview.md)
