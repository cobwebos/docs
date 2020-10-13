---
title: Azure 安全基准的 Azure Resource Graph 安全基线
description: Azure Resource Graph 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a85fdf1e1e27b1ce5abb0c7890b717d48ba3bd3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230443"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Azure 安全基准的 Azure Resource Graph 安全基线

此安全基线将指南从 [Azure 安全基准](../../../security/benchmarks/overview.md)应用到 Azure Resource Graph。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“安全控件”分组，此类控件按适用于 Azure Resource Graph 的 Azure 安全基准和相关的指南定义。 排除了不适用于 Azure Resource Graph 的**控件**。 若要查看 Azure Resource Graph 如何完全映射到 Azure 安全基准，请参阅[完整的 Azure 虚拟网络安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。



## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../../../security/benchmarks/security-control-identity-access-control.md)。

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Resource Graph 根据 Azure 基于角色的访问控制 (Azure RBAC) 提供对资源类型和属性的访问权限。 定期审核并查看授予安全主体（用户、组和服务帐户）的访问权限，以确保查询返回相应资源的结果。

* [Azure Resource Graph 中的权限](../overview.md#permissions-in-azure-resource-graph)

* [如何使用 Azure 标识访问评审](../../../active-directory/governance/access-reviews-overview.md)


**Azure 安全中心监视**：是

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../../../security/benchmarks/security-control-data-protection.md)。

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 来控制对数据和资源的访问。 若要使用 Azure Resource Graph，你还必须拥有对想要查询的资源的相应权限。 此访问权限应该限制为只读，并且只应授予所需人员。

* [Azure Resource Graph 中的权限](../overview.md#permissions-in-azure-resource-graph)

* [如何配置 Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)


**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询和发现订阅、管理组和租户中所有受支持的资源。 确保你在租户中拥有适当的权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

* [如何使用 Azure Resource Graph 创建查询](../first-query-portal.md)

* [了解 Azure RBAC](../../../role-based-access-control/overview.md)


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。 使用 Azure Resource Graph 查询已获批 Azure 资源和更改历史记录（预览版），以查看快照和查看更改的内容。

* [使用 Azure Resource Graph 查询 Azure 资源](../first-query-portal.md)

* [获取资源更改](../how-to/get-resource-changes.md)


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Resource Graph 查询和发现订阅、管理组和租户中的资源。 确保环境中的所有 Azure 资源已获批准。

* [使用 Azure Resource Graph 查询 Azure 资源](../first-query-portal.md)

* [示例：Azure Resource Graph 的初学者查询](../samples/starter.md)


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../../../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../../../security/benchmarks/security-baselines-overview.md)
