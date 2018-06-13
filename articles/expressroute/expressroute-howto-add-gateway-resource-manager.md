---
title: 将虚拟网络网关添加到 ExpressRoute 的 VNet：PowerShell：Azure | Microsoft Docs
description: 本文指导将 VNet 网关添加到已为 ExpressRoute 创建的 Resource Manager VNet 中。
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: charwen
ms.openlocfilehash: 3aeddd03e0be548933775164ae790ba208fc13ae
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
ms.locfileid: "23012882"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>使用 PowerShell 配置 ExpressRoute 的虚拟网络网关
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [经典 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [视频 - Azure 门户](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

本文将演示为预先存在的 VNet 添加虚拟网络 (VNet) 网关、重设其大小并进行删除的步骤。 此配置的步骤专用于使用 Resource Manager 部署模型创建的、会在 ExpressRoute 配置中使用的 VNet。 有关 ExpressRoute 的虚拟网络网关和网关配置设置的详细信息，请参阅[关于 ExpressRoute 的虚拟网络网关](expressroute-about-virtual-network-gateways.md)。 


## <a name="before-beginning"></a>开始之前
确认已安装最新的 Azure PowerShell cmdlet。 如果尚未安装最新的 cmdlet，必须先安装，才能开始执行配置步骤。 有关详细信息，请参阅[安装和配置 Azure PowerShell](/powershell/azure/overview)。

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>后续步骤
创建 VNet 网关之后，可以将 VNet 链接到 ExpressRoute 线路。 请参阅[将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)。

