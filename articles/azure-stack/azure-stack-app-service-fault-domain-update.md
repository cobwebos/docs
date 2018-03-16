---
title: "Azure 堆栈上的 app Service： 容错域更新 |Microsoft 文档"
description: "如何重新 Azure 堆栈上的 Azure App Service 发布跨容错域"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 851747263879aa89fabe8b168876238a004ea8b2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>如何重新 Azure 堆栈上的 Azure App Service 发布跨容错域

*适用于：Azure Stack 集成系统*

利用 1802年更新中，Azure 堆栈现在支持工作负荷的分布跨容错域上，一项功能，这很关键的高可用性。

> [!IMPORTANT]
> 您必须已更新你的 Azure 堆栈集成系统到 1802 能够利用的容错域支持。  本文档仅适用于在 1802年更新之前完成的应用程序服务资源提供程序部署。  如果你部署 Azure 堆栈上的 App Service 是 1802年更新已应用于 Azure 堆栈后，资源提供程序已分布在容错域。
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>跨容错域重新平衡的应用程序服务资源提供程序

若要重新分发应用程序服务资源提供程序部署缩放集，必须执行以下步骤，为每个缩放集。  默认情况下 scaleset 名称是：

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> 如果你不具有任何实例部署在某些辅助层缩放集，你不需要重新平衡这些缩放集。  当你在将来缩放它们将正确平衡缩放集。
>
>

1. 浏览到 Azure 的堆栈管理员门户中虚拟机规模集。  作为 App Service 部署的一部分部署的现有缩放集将列出与实例计数信息。

    ![虚拟机缩放集 UX 中列出的 azure 应用程序服务缩放集][1]

2. 下一步横向扩展每个组中。  例如，如果你有三个现有实例规模集中你必须向外扩展到 6，以便将跨容错域设置的三个新实例。
    a. [设置 PowerShell 中的 Azure 堆栈管理员环境](azure-stack-powershell-configure-admin.md)b。 使用此示例向外缩放集扩展：
        ```powershell
                Login-AzureRMAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> 此步骤可能需要数小时才能完成，具体取决于角色的类型和实例数。
>
>

3. 监视应用程序服务管理角色边栏选项卡中的新角色实例的状态。  通过单击列表中的角色类型检查单个角色实例的状态

    ![在 Azure 堆栈角色上的 azure App Service][2]

4. 一个新的实例处于**准备**状态，返回到虚拟机规模集的边栏选项卡和**删除**旧实例。

5. 重复上述步骤**每个**虚拟机规模集。

## <a name="next-steps"></a>后续步骤

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)。

* [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
