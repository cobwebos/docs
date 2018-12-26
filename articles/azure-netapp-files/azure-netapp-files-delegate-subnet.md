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
ms.topic: get-started-article
ms.date: 11/13/2018
ms.author: b-juche
ms.openlocfilehash: fc8ec5c3bbe0157f5737e2eb757478357aa6e867
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414298"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>将子网委派给 Azure NetApp 文件 

必须将一个子网委派给 Azure NetApp 文件。   在创建卷时，需要指定委派的子网。

## <a name="about-this-task"></a>关于此任务
* 用于创建新子网的向导默认设置 /24 网络掩码，这将提供 251 个可用 IP 地址。 对于此服务，使用 /28 网络掩码就足够了，这将提供 16 个可用 IP 地址。
* 不能在委派的子网中指定网络安全组或服务终结点。 这样做会导致子网委派失败。
* 在每个 Azure 虚拟网络 (VNet) 中，只能将一个子网委派给 Azure NetApp 文件。
* 当前不支持从对等互连的虚拟网络访问卷。

## <a name="steps"></a>步骤 
1.  在 Azure 门户中，转到“虚拟网络”边栏选项卡，选择要用于 Azure NetApp 文件的虚拟网络。    

1. 从“虚拟网络”边栏选项卡中选择“子网”，单击“+子网”按钮。 

1. 在“添加子网”页面中完成以下必需字段来新建要用于 Azure NetApp 文件的子网：
    * **名称**：指定子网名称。
    * **地址范围**：指定 IP 地址范围。
    * **子网委派**：选择“Microsoft.NetApp/卷”。 

      ![子网委派](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
还可以在[为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)时创建和委派子网。 

## <a name="next-steps"></a>后续步骤  
* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
* [了解 Azure 服务的虚拟网络集成](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


