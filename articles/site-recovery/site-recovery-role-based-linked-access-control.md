---
title: 使用基于角色的访问控制管理 Azure Site Recovery | Microsoft Docs
description: 本文介绍如何应用基于角色的访问控制 (RBAC)，并利用它来管理 Azure Site Recovery 部署
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: d02ecc3293d0607dd3e19ade3c1d9087b544703b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>使用基于角色的访问控制管理 Azure Site Recovery

Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 进行细致的访问管理。 通过 RBAC 可分离团队中的职责，并按需仅向用户授予执行特定作业的特定访问权限。

Azure Site Recovery 提供了 3 个用于控制 Site Recovery 管理操作的内置角色。 详细了解 [Azure RBAC 内置角色](../role-based-access-control/built-in-roles.md)

* [Site Recovery 参与者](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - 此角色拥有管理恢复服务保管库中 Azure Site Recovery 操作所需的全部权限。 不过，拥有此角色的用户既无法创建或删除恢复服务保管库，也无法向其他用户分配访问权限。 此角色最适合分配给灾难恢复管理员，这样他们就可以为应用程序或整个组织（视情况而定）启用和管理灾难恢复。
* [Site Recovery 操作员](../role-based-access-control/built-in-roles.md#site-recovery-operator) - 此角色有权执行和管理故障转移和故障回复操作。 拥有此角色的用户无法启用或禁用复制、无法创建或删除保管库，也无法注册新的基础结构或向其他用户分配访问权限。 此角色最适合分配给灾难恢复操作员，这样他们就可以在实际或模拟灾难情形（如 DR 钻取）下，遵循应用程序所有者或 IT 管理员的指示，对虚拟机或应用程序进行故障转移。 灾难解决后，DR 操作员可以重新保护和故障回复虚拟机。
* [Site Recovery 读者](../role-based-access-control/built-in-roles.md#site-recovery-reader) - 此角色有权查看所有 Site Recovery 管理操作。 此角色最适合分配给 IT 监视主管，这样他们就可以在需要时监视当前保护状态并创建支持票证。

若要定义自己的角色以便实现进一步控制，请参阅如何[在 Azure 中生成自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>为新虚拟机启用复制时所需的权限
使用 Azure Site Recovery 将新虚拟机复制到 Azure 时，会验证相关用户的访问级别，确保用户具有使用提供给 Site Recovery 的 Azure 资源所需的权限。

若要为新虚拟机启用复制，用户必须具有：
* 在所选资源组中创建虚拟机的权限
* 在所选虚拟网络中创建虚拟机的权限
* 写入所选存储帐户的权限

用户需要以下权限来完成新虚拟机的复制。

> [!IMPORTANT]
>确保根据用于资源部署的部署模型（Resource Manager/经典）添加相关权限。

| **资源类型** | 部署模型 | 权限 |
| --- | --- | --- |
| 计算 | 资源管理器 | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | 经典 | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| 网络 | 资源管理器 | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | 经典 | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| 存储 | 资源管理器 | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | 经典 | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| 资源组 | 资源管理器 | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

考虑分别为 Resource Manager 和经典部署模型使用[内置角色](../role-based-access-control/built-in-roles.md)“虚拟机参与者”和“经典虚拟机参与者”。

## <a name="next-steps"></a>后续步骤
* [基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)：Azure 门户中的 RBAC 入门。
* 了解如何通过以下方式管理访问权限：
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [基于角色的访问控制故障排除](../role-based-access-control/troubleshooting.md)：获取解决常见问题的建议。
