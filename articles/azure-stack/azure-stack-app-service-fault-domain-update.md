---
title: Azure Stack 上的应用服务：容错域更新 | Microsoft Docs
description: 如何跨容错域在 Azure Stack 上重新分配 Azure 应用服务
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 42adef66fb1b1141ab44aab3a1ccdaae022202b5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150968"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>如何跨容错域在 Azure Stack 上重新分配 Azure 应用服务

*适用于：Azure Stack 集成系统*

1802 更新推出后，Azure Stack 现在支持跨容错域分配工作负荷，这对于高可用性而言是一项很重要的功能。

> [!IMPORTANT]
> 必须将 Azure Stack 集成系统更新为 1802 才能利用容错域支持。  本文档仅适用于在 1802 更新之前已完成的应用服务资源提供程序部署。  如果在将 1802 更新应用到 Azure Stack 之后才部署 Azure Stack 上的应用服务，则资源提供程序已跨容错域分配。
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>跨容错域重新均衡应用服务资源提供程序

若要为应用服务资源提供程序重新分配部署的规模集，必须针对每个规模集执行以下步骤。  默认情况下，规模集名称为：

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> 如果某些辅助角色层规模集中未部署任何实例，则无需重新均衡这些规模集。  以后在缩放规模集时，规模集将会正确均衡。
>
>

1. 在 Azure Stack 管理员门户中浏览到“虚拟机规模集”。  部署为应用服务部署一部分的现有规模集将连同实例计数信息一起列出。

    ![列在虚拟机规模集 UX 中的 Azure 应用服务规模集][1]

2. 接下来横向扩展每个规模集。  例如，如果规模集中有 3 个现有实例，则必须横向扩展为 6 个，以便跨容错域预配 3 个新实例。
    a. [在 PowerShell 中设置 Azure Stack 管理环境](azure-stack-powershell-configure-admin.md) b. 使用以下示例来横向扩展规模集：
        ```powershell
                Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> 根据角色类型和实例数目，此步骤可能需要几小时才能完成。
>
>

3. 在“应用服务管理角色”边栏选项卡中监视新角色实例的状态。  在列表中单击角色类型，检查各个角色实例的状态

    ![Azure Stack 上的 Azure 应用服务角色][2]

4. 新实例进入“就绪”状态后，请返回“虚拟机规模集”边栏选项卡并**删除**旧实例。

5. 针对**每个**虚拟机规模集重复上述步骤。

## <a name="next-steps"></a>后续步骤

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)。

* [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
