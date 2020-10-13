---
title: 将子网委派给 Azure NetApp 文件 | Microsoft Docs
description: 了解如何向 Azure NetApp 文件委托子网。 创建卷时，请指定委托子网。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: bb3d1fd49c2623ff6dcbe8a19ae8c8ca3b46425a
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930622"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>将子网委派给 Azure NetApp 文件 

必须将一个子网委派给 Azure NetApp 文件。   在创建卷时，需要指定委派的子网。

## <a name="considerations"></a>注意事项

* 用于创建新子网的向导默认设置 /24 网络掩码，这将提供 251 个可用 IP 地址。 使用提供11个可用 IP 地址的/28 网络掩码足以满足服务的需求。
* 在每个 Azure 虚拟网络 (VNet) 中，只能将一个子网委派给 Azure NetApp 文件。   
   Azure 使你能够在 VNet 中创建多个委托子网。  但是，如果使用多个委托子网，则任何创建新卷的尝试都将失败。  
   VNet 中只能有一个委托子网。 NetApp 帐户可以将卷部署到多个 Vnet 中，每个都有其自己的委托子网。  
* 不能在委派的子网中指定网络安全组或服务终结点。 这样做会导致子网委派失败。
* 当前不支持从全局对等互连虚拟网络访问卷。
* 在 Azure NetApp 文件的委派子网中，不支持[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes) (udr) 和网络安全组 (nsg) 。 但是，你可以将 UDR 和 NSG 应用于其他子网，即使这些子网与委派给 Azure NetApp 文件的子网位于同一 VNet 中也可如此。  
   Azure NetApp 文件创建到委托子网的系统路由。 如果需要进行故障排除，路由将显示在路由表中的 **有效路由** 中。

## <a name="steps"></a>步骤

1.  中转到 Azure 门户中的 " **虚拟网络** " 边栏选项卡，并选择要用于 Azure NetApp 文件的虚拟网络。    

1. 从“虚拟网络”边栏选项卡中选择“子网”****，单击“+子网”按钮。**** 

1. 在“添加子网”页面中完成以下必需字段来新建要用于 Azure NetApp 文件的子网：
    * **名称**：指定子网名称。
    * **地址范围**：指定 IP 地址范围。
    * **子网委托**：选择 " **Microsoft"。** 

      ![子网委派](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
还可以在[为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)时创建和委派子网。 

## <a name="next-steps"></a>后续步骤

* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
* [了解 Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)