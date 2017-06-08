---
title: "使用基于角色的访问控制管理 Azure Site Recovery | Microsoft Docs"
description: "本文介绍如何应用基于角色的访问控制 (RBAC)，并利用它来管理 Azure Site Recovery 部署"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: manayar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 08a4d0fa673a37c61e57daed66ab6768e0276ca8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/12/2017


---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>使用基于角色的访问控制管理 Azure Site Recovery

Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 进行细致的访问管理。 通过 RBAC 可分离团队中的职责，并按需仅向用户授予执行特定作业的特定访问权限。

Azure 提供各种内置角色来控制资源管理操作。 详细了解 [Azure RBAC 内置角色](../active-directory/role-based-access-built-in-roles.md)

若要定义自己的角色以便实现进一步控制，请参阅如何[在 Azure 中生成自定义角色](../active-directory/role-based-access-control-custom-roles.md)。

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

考虑分别为 Resource Manager 和经典部署模型使用[内置角色](../active-directory/role-based-access-built-in-roles.md)“虚拟机参与者”和“经典虚拟机参与者”。

## <a name="next-steps"></a>后续步骤
* [基于角色的访问控制](../active-directory/role-based-access-control-configure.md)：Azure 门户中的 RBAC 入门。
* 了解如何通过以下方式管理访问权限：
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [REST API](../active-directory/role-based-access-control-manage-access-rest.md)
* [基于角色的访问控制故障排除](../active-directory/role-based-access-control-troubleshooting.md)：获取解决常见问题的建议。

