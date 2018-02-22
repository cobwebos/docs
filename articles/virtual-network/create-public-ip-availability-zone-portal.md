---
title: "使用 Azure 门户创建分区的公共 IP 地址 | Microsoft Docs"
description: "使用 Azure 门户在可用性区域中创建公共 IP 地址。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 0e796c8be0a5db8cb67e4a091fa51ed89920852c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>使用 Azure 门户在可用性区域中创建公共 IP 地址

可以在 Azure 可用性区域（预览）中部署公共 IP 地址。 可用性区域是 Azure 区域中物理上独立的区域。 了解如何：

> * 在可用性区域中创建公共 IP 地址
> * 标识在可用性区域中创建的相关资源

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 可用性区域处于预览状态，已准备好在开发和测试方案中使用。 可为选择 Azure 资源、区域和 VM 大小系列提供支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。

## <a name="log-in-to-azure"></a>登录 Azure

登录 Azure 门户 ( https://portal.azure.com )。 

## <a name="create-a-zonal-public-ip-address"></a>创建分区的公共 IP 地址

1. 在 Azure 门户的左上角单击“创建资源”。
2. 选择“网络”，然后选择“公共 IP 地址”。
3. 输入或选择以下设置的值，选择订阅，接受其余设置的默认值，然后单击“创建”：

    |设置|值|
    |---|---|
    |SKU| **基本**：使用静态或动态分配方法分配。 可分配到可指定公共 IP 地址的任何 Azure 资源，例如网络接口、VPN 网关、应用程序网关和面向 Internet 的负载均衡器。 可以将公共 IP 地址分配给“可用性区域”设置中的特定区域。 非区域冗余。 若要详细了解可用性区域，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 **标准**：只能使用静态分配方法分配。 可分配到网络接口或面向 Internet 的标准负载均衡器。 如果将公共 IP 地址分配给面向 Internet 的标准负载均衡器，必须选择“标准”。 有关 Azure 负载均衡器 SKU 的详细信息，请参阅 [Azure 负载均衡器标准 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。 默认情况下区域冗余。 可以按区域创建，并在特定的可用性区域中有保障。 标准 SKU 以预览版提供。 创建标准 SKU 公共 IP 地址之前，必须先注册预览版。 若要注册预览版，请参阅[注册标准 SKU 预览版](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up)。 标准 SKU 只能在支持的位置中创建。  有关支持的位置（区域）的列表，请参阅[区域可用性](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability)；有关其他区域支持，请密切关注 [Azure 虚拟网络更新](https://azure.microsoft.com/updates/?product=virtual-network)页。|   
    |名称|该名称在所选的资源组中必须唯一。|
    |资源组|单击“新建”，并输入 myResourceGroup|
    |Location|欧洲西部|
    |可用性区域|如果选择了**标准** SKU，可以选择“区域冗余”（如果想要 IP 地址在所有区域上可复原）。 如果选择**基本** SKU，则 IP 地址并非在所有区域上可复原。 无论选择哪种 SKU，都可以将地址分配给所选的特定区域。 |

    设置将显示在门户中，如下图所示：

    ![创建分区的公共 IP 地址](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> 将标准 SKU 公共 IP 地址分配到虚拟机的网络接口时，必须使用[网络安全组](security-overview.md#network-security-groups)显式允许预期流量。 创建并关联网络安全组且显式允许所需流量之后，才可与资源通信。

## <a name="next-steps"></a>后续步骤

- 了解有关[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)的详细信息
- 了解有关[公共 IP 地址](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的详细信息