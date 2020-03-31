---
title: 连接到 Azure 实验室服务中的对等网络 |微软文档
description: 了解如何将实验室网络与其他网络作为对等体连接。 例如，将本地学校/大学网络与 Azure 中的实验室虚拟网络连接。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: d2115b1dc7e9f3150e44eb5ee9417e88ebeaa279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370836"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>将实验室网络与 Azure 实验室服务中的对等虚拟网络连接 
本文提供有关将实验室网络与其他网络对等的信息。 

## <a name="overview"></a>概述
使用虚拟网络对等互连可以无缝连接 Azure 虚拟网络。 建立对等互连后，出于连接目的，两个虚拟网络会显示为一个。 对等虚拟网络中虚拟机之间的流量通过 Microsoft 骨干基础结构路由，就像流量通过专用 IP 地址在同一虚拟网络中的虚拟机之间路由一样。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

在某些情况下，您可能需要将实验室网络与对等虚拟网络连接，包括以下方案：

- 实验室中的虚拟机具有连接到本地许可证服务器以获取许可证的软件
- 实验室中的虚拟机需要访问大学网络共享上的数据集（或任何其他文件）。 

某些本地网络通过[ExpressRoute](../../expressroute/expressroute-introduction.md)或[虚拟网络网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)连接到 Azure 虚拟网络。 这些服务必须在 Azure 实验室服务之外设置。 要了解有关使用 ExpressRoute 将本地网络连接到 Azure 的详细信息，请参阅[ExpressRoute 概述](../../expressroute/expressroute-introduction.md)。 对于使用虚拟网络网关的本地连接，网关、指定的虚拟网络和实验室帐户必须都位于同一区域。

> [!NOTE]
> 创建与实验室帐户对等的 Azure 虚拟网络时，了解虚拟网络区域如何影响创建教室实验室的位置非常重要。  有关详细信息，请参阅管理员指南有关[区域\位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regions-or-locations)的部分。

## <a name="configure-at-the-time-of-lab-account-creation"></a>在创建实验室帐户时进行配置
在新的实验室帐户创建期间，可以选择在 **"高级**"选项卡上的**对等虚拟网络**下拉列表中显示的现有虚拟网络。所选虚拟网络连接到实验室帐户下创建的实验室（对等）。 进行此更改后创建的所有实验室中的虚拟机都将有权访问对等式虚拟网络上的资源。 

![选择要对等的 VNet](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> 有关创建实验室帐户的详细分步说明，请参阅[设置实验室帐户](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>创建实验室后配置
如果在创建实验室帐户时未设置对等网络，则可以从**实验室帐户**页的 **"实验室配置**"选项卡启用相同的属性。 对此设置所做的更改仅适用于更改后创建的实验室。 如您在映像中看到的，您可以为实验室帐户中的实验室启用或禁用**对等虚拟网络**。 

![创建实验室后启用或禁用 VNet 对等互连](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

当您为**对等虚拟网络**字段选择虚拟网络时，将禁用"**允许实验室创建者选择实验室位置**"选项。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域，以便它们与对等虚拟网络中的资源进行连接。 

> [!IMPORTANT]
> 此设置更改仅适用于进行更改后创建的实验室，不适用于现有实验室。 


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [允许实验室创建者选取实验室位置](allow-lab-creator-pick-lab-location.md)
- [将共享图像库附加到实验室](how-to-attach-detach-shared-image-gallery.md)
- [将用户添加为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)