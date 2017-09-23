---
title: "将 ExpressRoute 关联的虚拟网络从经典部署模型迁移到 Resource Manager 部署模型：Azure：PowerShell | Microsoft Docs"
description: "本页介绍如何在移动线路之后将关联的虚拟网络迁移到 Resource Manager 部署模型。"
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2017
ms.author: ganesr;cherylmc
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 964ea38569062a7127f60dd6309b328db263bf6f
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017

---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>将 ExpressRoute 关联的虚拟网络从经典部署模型迁移到 Resource Manager 部署模型

本文介绍如何在移动 ExpressRoute 线路后将 Azure ExpressRoute 关联的虚拟网络从经典部署模型迁移到 Azure Resource Manager 部署模型。 


## <a name="before-you-begin"></a>开始之前
* 验证你是否有最新版本的 Azure PowerShell 模块。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。
* 在开始配置之前，请务必查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。
* 查看[将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-move.md)中提供的信息。 请确保你完全了解限制和局限性。
* 验证该线路是否在经典部署模型中完全正常运行。
* 确保拥有一个在 Resource Manager 部署模型中创建的资源组。
* 查看以下资源迁移文档：

    * [平台支持的从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [有关平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移的技术深入探讨](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [常见问题解答：平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [查看最常见的迁移错误和缓解措施](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>支持的和不支持的方案

* 可以在不停机的情况下，将 ExpressRoute 线路从经典环境迁移到 Resource Manager 环境。 可以在不停机的情况下，将 ExpressRoute 线路从经典环境转移到 Resource Manager 环境。 按照[使用 PowerShell 将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-howto-move-arm.md)中的说明进行操作。 这是将连接的资源转移到虚拟网络的先决条件。
* 可以在不停机的情况下，将同一订阅中的虚拟网络、网关，以及附加到 ExpressRoute 线路的虚拟网络中的关联部署迁移到 Resource Manager 环境。 可以按照后面描述的步骤来迁移各种资源，例如虚拟网络、网关以及部署在虚拟网络中的虚拟机。 必须确保虚拟网络配置正确，然后才能进行迁移。 
* 若要完成虚拟网络、网关以及处于虚拟网络中但与 ExpressRoute 线路不属同一订阅的关联部署的迁移，则需停机一段时间。 文档最后一部分介绍了在迁移资源时必须执行的步骤。
* 无法迁移使用 ExpressRoute 网关和 VPN 网关的虚拟网络。

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型
必须先将 ExpressRoute 线路从经典环境转移到 Resource Manager 环境，然后才能尝试迁移附加到 ExpressRoute 线路的资源。 若要完成此任务，请参阅以下文章：

* 查看[将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-move.md)中提供的信息。
* [使用 Azure PowerShell 将线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-howto-move-arm.md)。
* 使用 Azure 服务管理门户。 可以按照工作流[创建新的 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)并选择导入选项。 

此操作不涉及停机。 只要迁移正在进行，就可以继续在本地和 Microsoft 之间传输数据。

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>迁移虚拟网络、网关和关联的部署

迁移所需步骤取决于资源是位于同一订阅中还是不同订阅中，还是两种情况都存在。

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>迁移与 ExpressRoute 线路属于同一订阅的虚拟网络、网关和关联的部署
此部分介绍了如何执行相关步骤，以便迁移与 ExpressRoute 线路属于同一订阅的虚拟网络、网关和关联的部署。 无需停机即可进行此迁移。 在整个迁移过程中，仍然可以使用所有资源。 正在进行迁移时，管理平面是锁定的。 

1. 确保已将 ExpressRoute 线路从经典环境转移到 Resource Manager 环境。
2. 确保已针对迁移进行了相应的虚拟网络准备。
3. 注册订阅，以便进行资源迁移。 若要针对资源迁移来注册订阅，请使用以下 PowerShell 代码片段：

  ```powershell 
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. 验证、准备和迁移。 若要转移虚拟网络，请使用以下 PowerShell 代码片段：

  ```powershell
  Move-AzureVirtualNetwork -Prepare $vnetName  
  Move-AzureVirtualNetwork -Commit $vnetName
  ```

  也可通过运行以下 PowerShell cmdlet 来中止迁移：

  ```powershell
  Move-AzureVirtualNetwork -Abort $vnetName
  ```

## <a name="next-steps"></a>后续步骤
* [平台支持的从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [有关平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移的技术深入探讨](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [常见问题解答：平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [查看最常见的迁移错误和缓解措施](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

