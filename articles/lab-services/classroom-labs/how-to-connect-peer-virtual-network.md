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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 9e53b6bdb041bfac5a82ed607b75b25ab0513f57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187998"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>在 Azure 实验室服务中将实验室的网络与对等虚拟网络连接

本文提供了有关将实验室网络与其他网络对等互连的信息。

## <a name="overview"></a>概述

使用虚拟网络对等互连可以无缝连接 Azure 虚拟网络。 建立对等互连后，出于连接目的，两个虚拟网络会显示为一个。 对等互连虚拟网络中虚拟机之间的流量通过 Microsoft 主干基础结构路由，非常类似于只通过专用 IP 地址在同一虚拟网络中的虚拟机之间路由流量。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

在某些情况下，你可能需要将实验室的网络与对等虚拟网络连接，其中包括以下各项：

- 实验室中的虚拟机具有连接到本地许可证服务器以获取许可证的软件。
- 实验室中的虚拟机需要访问大学网络共享上的数据集（或任何其他文件）。

某些本地网络通过[ExpressRoute](../../expressroute/expressroute-introduction.md)或[虚拟网络网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)连接到 Azure 虚拟网络。 这些服务必须在 Azure 实验室服务之外设置。 若要详细了解如何使用 ExpressRoute 将本地网络连接到 Azure，请参阅[ExpressRoute 概述](../../expressroute/expressroute-introduction.md)。 对于使用虚拟网关的本地连接，网关、指定的虚拟网络和实验室帐户必须都位于同一区域。

> [!NOTE]
> 创建将与实验室帐户对等互连的 Azure 虚拟网络时，务必要了解虚拟网络的区域如何影响创建教室实验室。  有关详细信息，请参阅[regions\locations](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)上的管理员指南部分。

## <a name="configure-at-the-time-of-lab-account-creation"></a>创建实验室帐户时进行配置

创建新的[实验室帐户](tutorial-setup-lab-account.md)时，可以在 "**高级**" 选项卡上的 "**对等虚拟网络**" 下拉列表中选择一个现有虚拟网络。 此列表将仅显示与实验室帐户位于同一区域的虚拟网络。 所选虚拟网络已连接（对等互连）到实验室帐户下创建的实验室。  在进行此更改后，在实验室中创建的所有虚拟机都可以访问对等互连虚拟网络中的资源。

![选择 VNet 到对等](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>地址范围

还可以选择为实验室提供虚拟机的**地址范围**。  "**地址范围**" 属性仅适用于为实验室启用了**对等互连虚拟网络**的情况。  如果提供了地址范围，则将在该地址范围内创建实验室帐户下的实验室中的所有虚拟机。 地址范围应为 CIDR 表示法（例如 10.20.0.0/20），且不与任何现有地址范围重叠。  提供地址范围时，请考虑要创建的*实验室*数量，并提供一个地址范围来满足这一点。 实验室服务每个实验室最多假设有512的虚拟机。  例如，使用 "/23" 的 ip 范围只能创建一个实验室。  具有 "/21" 的范围将允许创建四个实验室。

如果未指定**地址范围**，则在创建要与虚拟网络对等互连的虚拟网络时，实验室服务将使用 Azure 提供的默认地址范围。  范围通常类似于 .0/16。  这可能会导致 ip 范围重叠，因此请确保在实验室设置中指定和寻址范围，或检查要对等互连的虚拟网络的地址范围。

## <a name="configure-after-the-lab-is-created"></a>创建实验室后进行配置

如果在创建实验室帐户时未设置对等网络，则可以从 "**实验室帐户**" 页的 "**实验室配置**" 选项卡中启用相同的属性。 对此设置所做的更改仅适用于在更改后创建的实验室。 如图中所示，可以在实验室帐户中为实验室启用或禁用**对等虚拟网络**。

![创建实验室后启用或禁用 VNet 对等互连](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

为**对等虚拟网络**字段选择虚拟网络后，将禁用 "**允许实验室创建者选取实验室位置**" 选项。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域，才能与对等虚拟网络中的资源进行连接。

> [!IMPORTANT]
> 对等互连虚拟网络设置仅适用于在进行更改后创建的实验室，不适用于现有实验室。

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [允许实验室创建者选取实验室位置](allow-lab-creator-pick-lab-location.md)
- [将共享映像库附加到实验室](how-to-attach-detach-shared-image-gallery.md)
- [添加用户作为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)
