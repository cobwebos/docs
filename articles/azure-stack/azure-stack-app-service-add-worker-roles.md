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
ms.openlocfilehash: 680cb70777574d0ed88c5f83fb0a6fa20263b951
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
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
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > 此步骤可能需要数小时才能完成，具体取决于角色的类型和实例数。
   >
   >

3. 监视应用程序服务管理中的新角色实例的状态，若要检查单个角色实例的状态，请单击列表中的角色类型。

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>添加其他工作进程直接在应用程序服务资源提供程序管理员联系。

1. 服务管理员身份登录到 Azure 堆栈管理门户。

2. 浏览到**应用程序服务**。

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. 单击“角色”。 你看到的所有部署的应用程序服务角色的明细。

4. 右键单击你想要缩放，然后单击类型的行**ScaleSet**。

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. 单击**缩放**，选择你想要扩展到，然后单击的实例数**保存**。

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Azure 堆栈上的 app Service 现在将添加更多的虚拟机、 将其配置，安装所有所需的软件，和将其标记为准备此过程完成后。 此过程可能需要大约 80 分钟。

7. 你可以通过查看中的辅助角色监视新的角色的就绪状态的进度**角色**边栏选项卡。

## <a name="result"></a>结果

完全部署，并准备就绪后，工作人员变为可供用户部署其工作负荷到它们。 以下举例说明的多个可用的定价层默认情况下。 如果有可用的辅助角色有关的特定辅助角色层，用于选择相应的定价层选项不可用。

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> 若要向外管理扩展，前端或发布服务器角色添加必须横向扩展相应的角色类型。 
>
>

若要向外管理、 前端或发布服务器角色扩展，请按照相同的步骤选择相应的角色类型。 控制器不作为缩放集部署并因此，应该两个部署在所有的生产部署的安装时间。

### <a name="next-steps"></a>后续步骤

[配置部署源](azure-stack-app-service-configure-deployment-sources.md)
