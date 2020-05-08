---
title: 将子网委派给 Azure NetApp 文件 | Microsoft Docs
description: 介绍了如何将子网委派给 Azure NetApp 文件。
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
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: b-juche
ms.openlocfilehash: 5f36e40091ada27f411adc2ffa78b6d4a58f8cca
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791402"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>将子网委派给 Azure NetApp 文件 

必须将一个子网委派给 Azure NetApp 文件。   在创建卷时，需要指定委派的子网。

## <a name="considerations"></a>注意事项
* 用于创建新子网的向导默认设置 /24 网络掩码，这将提供 251 个可用 IP 地址。 对于此服务，使用 /28 网络掩码就足够了，这将提供 16 个可用 IP 地址。
* 在每个 Azure 虚拟网络 (VNet) 中，只能将一个子网委派给 Azure NetApp 文件。   
   Azure 使你能够在 VNet 中创建多个委托子网。  但是，如果使用多个委托子网，则任何创建新卷的尝试都将失败。  
   VNet 中只能有一个委托子网。 NetApp 帐户可以将卷部署到多个 Vnet 中，每个都有其自己的委托子网。  
* 不能在委派的子网中指定网络安全组或服务终结点。 这样做会导致子网委派失败。
* 当前不支持从全局对等互连虚拟网络访问卷。
* 不支持在将地址前缀（目标）到委托给 Azure NetApp 文件的子网的 VM 子网上创建[用户定义的自定义路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)。 这样做会影响 VM 连接。

## <a name="steps"></a>步骤 
1.  在 Azure 门户中，转到“虚拟网络”**** 边栏选项卡，选择要用于 Azure NetApp 文件的虚拟网络。    

1. 从“虚拟网络”边栏选项卡中选择“子网”****，单击“+子网”按钮。**** 

1. 在“添加子网”页面中完成以下必需字段来新建要用于 Azure NetApp 文件的子网：
    * **名称**：指定子网名称。
    * **地址范围**：指定 IP 地址范围。
    * **子网委托**：选择 " **Microsoft"。** 

      ![子网委派](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
还可以在[为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)时创建和委派子网。 

## <a name="next-steps"></a>后续步骤  
* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
* [了解 Azure 服务的虚拟网络集成](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


