---
title: "使用基于角色的访问控制来管理 Azure Site Recovery |Microsoft 文档"
description: "本文介绍如何应用并使用基于角色的访问控制 (RBAC) 来管理 Azure Site Recovery 部署"
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
ms.date: 06/28/2017
ms.author: manayar
ms.openlocfilehash: 9dd74014bf05234a83c7678b67b42b96cd8b8d64
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>使用基于角色的访问控制来管理 Azure Site Recovery 部署

Azure 基于角色的访问控制 (RBAC) 启用 Azure 的精细访问权限管理。 使用 RBAC，可以分离团队内的职责，并仅特定访问权限授予用户所需执行特定作业。

Azure Site Recovery 提供了 3 的内置角色，以控制站点恢复管理操作。 了解详细信息，请参阅[Azure RBAC 内置角色](../active-directory/role-based-access-built-in-roles.md)

* [站点恢复参与者](../active-directory/role-based-access-built-in-roles.md#site-recovery-contributor)-此角色具有管理恢复服务保管库中的 Azure Site Recovery 操作所需的所有权限。 用户使用此角色，但是，不能创建或删除恢复服务保管库或分配给其他用户的访问权限。 此角色最适合用于灾难恢复管理员可以启用和管理应用程序或整个组织的灾难恢复，具体情况而定。
* [站点恢复运算符](../active-directory/role-based-access-built-in-roles.md#site-recovery-operator)-此角色具有的权限来执行和管理器故障转移和故障回复操作。 具有此角色的用户不能启用或禁用复制、 创建或删除保管库、 注册新的基础结构或可以分配给其他用户的访问权限。 此角色在最适合用于灾难恢复，则操作员可以故障转移虚拟机或应用程序的应用程序所有者和一个实际或模拟灾难情况，如 DR 中的 IT 管理员指示时钻取。 Post 分辨率高达灾难，DR 运算符可以重新保护和故障回复虚拟机。
* [站点恢复读取器](../active-directory/role-based-access-built-in-roles.md#site-recovery-reader)-此角色有权查看所有站点恢复管理操作。 此角色是最适合的 IT 监视主管，也可以监视保护的当前状态并发出支持票证，如果需要。

如果你寻找来定义自己的角色进行更多控制，请参阅如何[生成自定义角色](../active-directory/role-based-access-control-custom-roles.md)在 Azure 中。

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>新的虚拟机启用复制到所需的权限
当新的虚拟机复制到 Azure 中使用 Azure Site Recovery 中时，将验证关联的用户的访问级别，以确保用户具有所需的权限，用于提供到 Site Recovery 的 Azure 资源。

若要启用的新的虚拟机复制，用户必须具有：
* 在所选的资源组中创建虚拟机权限
* 在所选的虚拟网络中创建虚拟机权限
* 要写入到所选的存储帐户的权限

用户需要以下权限的新的虚拟机完成复制。

> [!IMPORTANT]
>确保将每个部署模型添加相关的权限 (资源管理器 / 经典) 用于资源部署。

| **资源类型** | **部署模型** | **Permission** |
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

请考虑使用虚拟机参与者和经典虚拟机参与者[内置角色](../active-directory/role-based-access-built-in-roles.md)资源管理器和经典部署模型分别。

## <a name="next-steps"></a>后续步骤
* [基于角色的访问控制](../active-directory/role-based-access-control-configure.md)： 在 Azure 门户中开始使用 RBAC。
* 了解如何使用管理访问权限：
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [REST API](../active-directory/role-based-access-control-manage-access-rest.md)
* [基于角色的访问控制的故障排除](../active-directory/role-based-access-control-troubleshooting.md)： 获取有关如何解决常见的问题的建议。
