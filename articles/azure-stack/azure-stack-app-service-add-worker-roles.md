---
title: "横向扩展应用程序服务-Azure 堆栈中的辅助角色 |Microsoft 文档"
description: "缩放 Azure 堆栈应用程序服务的详细的指南"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: d6471796863a80e69fdaf740b68fb27d59503453
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Azure 堆栈上的 app Service： 添加更多的基础结构或辅助角色

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*  

本文档提供有关如何在 Azure 堆栈的基础结构和辅助角色上缩放 App Service 的说明。 它不包含用于创建其他辅助角色来支持的任何大小的应用程序的步骤。

> [!NOTE]
> 如果你的 Azure 堆栈环境不具有多个 96 GB RAM 的情况下，你可能会遇到困难增加额外容量。

默认情况下，Azure 堆栈上的 app Service 支持免费和共享辅助角色层。 若要添加其他辅助角色层，你需要添加更多的辅助角色。

如果你不确定什么与 Azure 堆栈安装上的默认值 App Service 一起部署，你可以查看中的其他信息[App Service 上 Azure 堆栈概述](azure-stack-app-service-overview.md)。

Azure 堆栈上的 azure App Service 部署使用虚拟机规模集的所有角色，并因此可充分利用此工作负荷的缩放功能。 因此，所有辅助角色层缩放是通过应用程序服务管理员联系。

> [!IMPORTANT]
> 目前不可能要在门户中标识的 Azure 堆栈发行说明中缩放虚拟机规模集，因此使用 PowerShell 示例来向外扩展。
>
>

## <a name="add-additional-workers-with-powershell"></a>添加使用 PowerShell 的其他工作进程

1. [安装 PowerShell 中的 Azure 堆栈管理环境](azure-stack-powershell-configure-admin.md)
2. 使用此示例向外缩放集扩展：
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRMAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if you VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
  
    '''

> [!NOTE]
> This step can take a number of hours to complete depending on the type of role and the number of instances.
>
>

3. Monitor the status of the new role instances in the App Service Administration, to check the status of an individual role instance click the role type in the list.

## Add additional workers directly within the App Service Resource Provider Admin.

1. Log in to the Azure Stack administration portal as the service administrator.

2. Browse to **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Click **Roles**. Here you see the breakdown of all App Service roles deployed.

4. Right click on the row of the type you want to scale and then click **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Click **Scaling**, select the number of instances you want to scale to, and then click **Save**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service on Azure Stack will now add the additional VMs, configure them, install all the required software, and mark them as ready when this process is complete. This process can take approximately 80 minutes.

7. You can monitor the progress of the readiness of the new roles by viewing the workers in the **Roles** blade.

## Result

After they are fully deployed and ready, the workers become available for users to deploy their workload onto them. The following shows an example of the multiple pricing tiers available by default. If there are no available workers for a particular worker tier, the option to choose the corresponding pricing tier is unavailable.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> To scale out Management, Front End or Publisher roles add you must scale out the corresponding role type. 
>
>

To scale out Management, Front End, or Publisher roles, follow the same steps selecting the appropriate role type. Controllers are not deployed as Scale Sets and therefore two should be deployed at Installation time for all production deployments.

### Next steps

[Configure deployment sources](azure-stack-app-service-configure-deployment-sources.md)
