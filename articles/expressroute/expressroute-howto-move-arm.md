---
title: "将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型：PowerShell：Azure | Microsoft 文档"
description: "本页介绍如何使用 PowerShell 将经典线路转移到 Resource Manager 部署模型。"
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 6d11b75fdd33260be3d975d9bc25fdac3cf22b49
ms.openlocfilehash: 73f42b25d667f07205e7e67556c367f1a0e6e215


---
# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model-using-powershell"></a>使用 PowerShell 将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型

若要在经典部署模型和 Resource Manager 部署模型中使用 ExpressRoute 线路，必须将该线路转移到 Resource Manager 部署模型。 下列各部分将引导你完成使用 PowerShell 转移线路的步骤。

## <a name="before-you-begin"></a>开始之前
* 确认具有最新版本的 Azure PowerShell 模块（至少 1.0 版）。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 在开始配置之前，请务必查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。
* 查看[将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-move.md)中提供的信息。 请确保你完全了解限制和局限性。
* 验证该线路是否在经典部署模型中完全正常运行。
* 确保拥有一个在 Resource Manager 部署模型中创建的资源组。

## <a name="move-an-expressroute-circuit"></a>转移 ExpressRoute 线路

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>步骤 1：从经典部署模型收集线路详细信息
登录到 Azure 经典环境并收集服务密钥。

1. 登录到你的 Azure 帐户。

        Add-AzureAccount

2. 选择适当的 Azure 订阅。

        Select-AzureSubscription "<Enter Subscription Name here>"

3. 为 Azure 和 ExpressRoute 导入 PowerShell 模块。

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

4. 使用下面的 cmdlet 获取所有 ExpressRoute 线路的服务密钥。 检索密钥后，复制要转移到 Resource Manager 部署模型的线路的**服务密钥**。

        Get-AzureDedicatedCircuit

### <a name="step-2-sign-in-and-create-a-resource-group"></a>步骤 2：登录并创建资源组
登录到 Resource Manager 环境并创建新的资源组。

1. 登录到 Azure Resource Manager 环境。

        Login-AzureRmAccount

2. 选择适当的 Azure 订阅。

        Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

3. 如果还没有资源组，请修改下面的代码段以创建新的资源组。

        New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>步骤 3：将 ExpressRoute 线路转移到 Resource Manager 部署模型
现在，你可以将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型。 在继续下一步之前，请先参阅[将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-move.md)中提供的信息。

若要转移线路，请修改并运行以下代码段：

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

> [!NOTE]
> 转移完成之后，列在前一个 cmdlet 中的新名称用于处理资源。 线路实质上已重命名。
> 

## <a name="modify-circuit-access"></a>修改线路访问权限

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>为两种部署模型启用 ExpressRoute 线路访问权限
将经典 ExpressRoute 线路转移到 Resource Manager 部署模型后，可以启用对这两种部署模型的访问权限。 运行以下 cmdlet 启用对两种部署模型的访问权限：

1. 获取线路详细信息。

        $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

2. 将“允许经典操作”设置为 TRUE。

        $ckt.AllowClassicOperations = $true

3. 更新线路。 成功完成此操作后，可以在经典部署模型中查看线路。

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

4. 运行以下 cmdlet 获取 ExpressRoute 线路的详细信息。 服务密钥必须已列出。 

        get-azurededicatedcircuit

5. 现在，你可以使用适用于经典 VNet 的经典部署模型命令以及适用于 Resource Manager VNet 的 Resource Manager 命令来管理到 ExpressRoute 线路的链接。 以下文章将引导你了解如何管理 ExpressRoute 线路的链接：

    * [在 Resource Manager 部署模型中将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
    * [在经典部署模型中将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>禁止 ExpressRoute 线路访问经典部署模型
运行以下 cmdlet 可禁止访问经典部署模型。

1. 获取 ExpressRoute 线路的详细信息。

        $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

2. 将“允许经典操作”设置为 FALSE。

        $ckt.AllowClassicOperations = $false

3. 更新线路。 成功完成此操作后，你将无法在经典部署模型中查看线路。

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>后续步骤

* [创建和修改 ExpressRoute 线路的路由](expressroute-howto-routing-arm.md)
* [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)




<!--HONumber=Feb17_HO1-->


