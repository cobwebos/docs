---
title: 在 Azure 实验室服务中连接到对等网络 | Microsoft Docs
description: 了解如何将实验室网络连接到其他对等网络。 例如，在 Azure 中将本地组织/大学网络连接到实验室的虚拟网络。
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 556a32a111149fe5ade3b11fee9c732c935de289
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592009"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>在 Azure 实验室服务中将实验室的网络连接到对等虚拟网络

本文提供了有关将实验室网络与其他网络对等互连的信息。

## <a name="overview"></a>概述

使用虚拟网络对等互连可以无缝连接 Azure 虚拟网络。 建立对等互连后，出于连接目的，两个虚拟网络会显示为一个。 对等虚拟网络中虚拟机之间的流量通过 Microsoft 主干基础结构路由，非常类似于只通过专用 IP 地址在同一虚拟网络中的虚拟机之间路由流量。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

在某些情况下，你可能需要将实验室的网络连接到对等虚拟网络，包括：

- 实验室中的虚拟机具有连接到本地许可证服务器以获取许可证的软件。
- 实验室中的虚拟机需要访问大学网络共享上的数据集（或任何其他文件）。

某些本地网络通过 [ExpressRoute](../../expressroute/expressroute-introduction.md) 或[虚拟网络网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)连接到 Azure 虚拟网络。 这些服务必须在 Azure 实验室服务之外设置。 若要详细了解如何使用 ExpressRoute 将本地网络连接到 Azure，请参阅 [ExpressRoute 概述](../../expressroute/expressroute-introduction.md)。 对于使用虚拟网关的本地连接，网关、指定的虚拟网络和实验室帐户必须都位于同一区域。

> [!NOTE]
> 创建将与实验室帐户对等互连的 Azure 虚拟网络时，必须了解虚拟网络的区域如何影响创建教室实验室的位置。  有关详细信息，请参阅 [regions\locations](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations) 上的管理员指南部分。

## <a name="configure-at-the-time-of-lab-account-creation"></a>创建实验室帐户时进行配置

在新的[实验室帐户创建](tutorial-setup-lab-account.md)过程中，可以选择显示在“高级”选项卡上的“对等虚拟网络”下拉列表中的现有虚拟网络 。此列表将仅显示与实验室帐户位于同一区域的虚拟网络。 所选虚拟网络已连接（对等互连）到实验室帐户下创建的实验室。  进行此更改后在实验室中创建的所有虚拟机都将有权访问对等虚拟网络中的资源。

![选择 VNet 到对等](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>地址范围

还可以选择为实验室的虚拟机提供地址范围。  仅在为实验室启用了“对等虚拟网络”时，“地址范围”属性才适用。 如果提供了地址范围，则实验室帐户下的实验室中的所有虚拟机都将在该地址范围内创建。 地址范围应使用 CIDR 表示法（例如，10.20.0.0/20），且不应与任何现有地址范围重叠。  提供地址范围时，请务必考虑将要创建的实验室数量，并提供可容纳该数量的地址范围。 实验室服务假定每个实验室最多有 512 个虚拟机。  例如，“/23”IP 范围只能创建一个实验室。  “/21”范围将允许创建四个实验室。

如果未指定“地址范围”，则在创建要与虚拟网络对等互连的虚拟网络时，实验室服务将使用 Azure 提供的默认地址范围。  范围通常类似于 10.x.0.0/16。  这可能会导致 IP 范围重叠，因此请确保在实验室设置中指定地址范围，或检查要对等互连的虚拟网络的地址范围。

> [!NOTE]
> 如果实验室帐户与虚拟网络对等互连，但 IP 地址范围太窄，则实验室创建可能会失败。 如果实验室帐户中的实验室过多（每个实验室使用 512 个地址），则可能会用尽地址范围中的空间。 
> 
> 如果实验室创建失败，请与实验室帐户所有者/管理员联系并请求增加地址范围。 管理员可以使用[为实验室帐户中的 VM 指定地址范围](#specify-an-address-range-for-vms-in-the-lab-account)部分中提到的步骤来增加地址范围。 

## <a name="configure-after-the-lab-account-is-created"></a>在创建实验室帐户后配置

如果在创建实验室帐户时未设置对等网络，则可以从“实验室帐户”页的“实验室配置”选项卡中启用相同属性 。 对此设置所做的更改仅适用于在更改后创建的实验室。 如图中所示，可以为实验室帐户中的实验室启用或禁用“对等虚拟网络”。

![在创建实验室后启用或禁用 VNet 对等互连](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

为“对等虚拟网络”字段选择虚拟网络时，“允许实验室创建者选取实验室位置”选项处于禁用状态 。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域中，才能与对等虚拟网络中的资源连接。

> [!IMPORTANT]
> 对等互连的虚拟网络设置仅适用于在更改后创建的实验室，不适用于现有实验室。


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>为实验室帐户中的 VM 指定地址范围
以下过程包含为实验室中的 VM 指定地址范围的步骤。 如果更新之前指定的范围，则修改后的地址范围仅适用于在更改后创建的 VM。 

指定地址范围时，应谨记以下一些限制。 

- 前缀必须小于或等于 23。 
- 如果虚拟网络对等互连到实验室帐户，则提供的地址范围不能与对等互连的虚拟网络中的地址范围重叠。

1. 在“实验室帐户”页上，选择左侧菜单中的“实验室设置”。
2. 对于“地址范围”字段中，指定将在实验室中创建的 VM 的地址范围。 此地址范围应当采用无类别域际路由 (CIDR) 表示法（示例：10.20.0.0/23）。 将在此地址范围内创建实验室中的虚拟机。
3. 在工具栏上选择“保存”。 

    ![配置地址范围](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [允许实验室创建者选取实验室位置](allow-lab-creator-pick-lab-location.md)
- [将共享映像库连接到实验室](how-to-attach-detach-shared-image-gallery.md)
- [添加用户作为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)
