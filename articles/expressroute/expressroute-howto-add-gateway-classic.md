---
title: "使用 PowerShell 为 ExpressRoute 配置 VNet 网关 | Microsoft Docs"
description: "在 ExpressRoute 配置中使用 PowerShell 配置经典部署模型 VNet 的 VNet 网关。"
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 2a0db85dc5699b362aa6a920cc26c40bdfdfc28d
ms.openlocfilehash: 1679bc2f2344ce3dd78599a1e6a8e2cc974ee905


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-classic-deployment-model-and-powershell"></a>使用经典部署模型和 PowerShell 为 ExpressRoute 配置虚拟网络网关
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [经典 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [视频 - Azure 门户](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

本文将指导你完成为预先存在的 VNet 添加、重设大小和删除虚拟网络 (VNet) 网关的步骤。 此配置的步骤专用于使用**经典部署模型**创建的、将在 ExpressRoute 配置中使用的 VNet。 

**关于 Azure 部署模型**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>开始之前
确认已安装此配置所需的 Azure PowerShell cmdlet（1.0.2 或更高版本）。 如果尚未安装 cmdlet，必须先安装，然后才能开始执行配置步骤。 有关安装 Azure PowerShell 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>后续步骤
创建 VNet 网关之后，可以将 VNet 链接到 ExpressRoute 线路。 请参阅[将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)。




<!--HONumber=Dec16_HO2-->


