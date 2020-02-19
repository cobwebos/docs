---
title: 在 Azure 实验室服务中连接到对等网络 |Microsoft Docs
description: 了解如何以对等互连的方式将实验室网络连接到另一个网络。 例如，将本地学校/大学网络与 Azure 中的实验室虚拟网络连接起来。
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
ms.openlocfilehash: 5e013011f81542aa279ba9276a6a1aac01eb9e41
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443172"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>在 Azure 实验室服务中将实验室的网络与对等虚拟网络连接 
本文提供了有关将实验室网络与其他网络对等互连的信息。 

## <a name="overview"></a>概述
利用虚拟网络对等互连，可无缝连接 Azure 虚拟网络。 建立对等互连后，出于连接目的，两个虚拟网络会显示为一个。 对等互连虚拟网络中虚拟机之间的流量通过 Microsoft 主干基础结构路由，非常类似于只通过专用 IP 地址在同一虚拟网络中的虚拟机之间路由流量。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

在某些情况下，你可能需要将实验室的网络与对等虚拟网络连接，其中包括以下各项：

- 实验室中的虚拟机具有连接到本地许可证服务器以获取许可证的软件
- 实验室中的虚拟机需要访问大学网络共享上的数据集（或任何其他文件）。 

某些本地网络通过[ExpressRoute](../../expressroute/expressroute-introduction.md)或[虚拟网络网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)连接到 Azure 虚拟网络。 这些服务必须在 Azure 实验室服务之外设置。 若要详细了解如何使用 ExpressRoute 将本地网络连接到 Azure，请参阅[ExpressRoute 概述](../../expressroute/expressroute-introduction.md)。 对于使用虚拟网关的本地连接，网关、指定的虚拟网络和实验室帐户必须都位于同一区域。

## <a name="configure-at-the-time-of-lab-account-creation"></a>创建实验室帐户时进行配置
创建新的实验室帐户时，可以在 "**高级**" 选项卡上的 "**对等虚拟网络**" 下拉列表中选择一个现有虚拟网络。所选虚拟网络已连接（对等互连）到实验室帐户下创建的实验室。 在进行此更改后，在实验室中创建的所有虚拟机都可以访问对等互连虚拟网络中的资源。 

![选择 VNet 到对等](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> 有关创建实验室帐户的详细分步说明，请参阅[设置实验室帐户](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>创建实验室后进行配置
如果在创建实验室帐户时未设置对等网络，则可以从 "**实验室帐户**" 页的 "**实验室配置**" 选项卡中启用相同的属性。 对此设置所做的更改仅适用于在更改后创建的实验室。 如图中所示，可以在实验室帐户中为实验室启用或禁用**对等虚拟网络**。 

![创建实验室后启用或禁用 VNet 对等互连](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

为**对等虚拟网络**字段选择虚拟网络后，将禁用 "**允许实验室创建者选取实验室位置**" 选项。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域，才能与对等虚拟网络中的资源进行连接。 

> [!IMPORTANT]
> 此设置更改仅适用于在进行更改后创建的实验室，不适用于现有实验室。 


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [允许 lab creator 选取实验室位置](allow-lab-creator-pick-lab-location.md)
- [将共享映像库附加到实验室](how-to-attach-detach-shared-image-gallery.md)
- [添加用户作为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)