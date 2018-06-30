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
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130364"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>如何跨容错域在 Azure Stack 上重新分配 Azure 应用服务

*适用于：Azure Stack 集成系统*

利用 1802年更新中，Azure 堆栈现在支持工作负荷的分布跨容错域上，一种功能，以实现高可用性至关重要。

>[!IMPORTANT]
>若要利用的容错域支持，必须到 1802年更新你的 Azure 堆栈集成系统。 本文档仅适用于 1802年更新前完成的应用程序服务资源提供程序部署。 如果你部署 Azure 堆栈上的 App Service，1802年更新已应用于 Azure 堆栈后，资源提供程序已分布在容错域。

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>跨容错域重新均衡应用服务资源提供程序

若要重新分发应用程序服务资源提供程序部署缩放集，必须为每个缩放集这篇文章中执行步骤。 默认情况下，scaleset 名称是：

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> 如果您没有在某些辅助层缩放集部署的实例，你不需要重新平衡这些缩放集。 以后在缩放规模集时，规模集将会正确均衡。

向外缩放集扩展，请执行以下步骤：

1. 登录到 Azure 的堆栈管理员门户。
2. 选择“更多服务”。
3. 在计算、 下选择**虚拟机规模集**。 部署为应用服务部署一部分的现有规模集将连同实例计数信息一起列出。 以下屏幕截图显示缩放集的一个示例。

      ![列在虚拟机规模集 UX 中的 Azure 应用服务规模集][1]

4. 横向扩展每个组。 例如，如果你有三个现有实例规模集中你必须向外扩展到 6 以便跨容错域部署的三个新实例。 下面的 PowerShell 示例出演示来向外缩放集扩展。

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >此步骤可能需要几个小时才能完成，具体取决于角色的类型和实例数。

5. 在**应用程序服务管理角色**，监视新的角色实例的状态。 若要检查角色实例的状态，在列表中选择的角色类型

    ![Azure Stack 上的 Azure 应用服务角色][2]

6. 新的角色实例的状态时**准备**，回到**虚拟机规模集**和**删除**旧的角色实例。

7. 针对**每个**虚拟机规模集重复上述步骤。

## <a name="next-steps"></a>后续步骤

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)。

* [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
