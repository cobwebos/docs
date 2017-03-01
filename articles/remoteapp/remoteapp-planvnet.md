---
title: "如何规划 Azure RemoteApp 集合的虚拟网络 | Microsoft Docs"
description: "了解如何规划 Azure RemoteApp 集合的虚拟网络。"
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: ad9aff0e-f374-49c0-951d-4a7be1c36de0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 71537845edcca85a7d0722dd32264da9f6b579e9


---
# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>如何规划 Azure RemoteApp 的虚拟网络
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

本文档介绍如何设置 Azure虚拟网络 (VNET) 和 Azure RemoteApp 的子网。 如果你不熟悉 Azure 虚拟网络，这项功能可帮助你将网络基础结构虚拟到云，并使用 Azure 和你的本地资源创建混合解决方案。 你可以在 [此处](../virtual-network/virtual-networks-overview.md) 阅读详细内容。

如果要在部署 Azure RemoteApp 的虚拟网络中为流量（传入和传出）定义安全策略，我们强烈建议为 Azure RemoteApp 创建单独的子网，使其与 Azure 虚拟网络中的其余部署分开。 有关如何在 Azure 虚拟网络子网上定义安全策略的详细信息，请阅读 [什么是网络安全组 (NSG)？](../virtual-network/virtual-networks-nsg.md)。

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>使用 Azure 虚拟网络的 Azure RemoteApp 集合的类型
下图显示了当你要使用虚拟网络时的两种不同的集合选项。

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>使用 VNET 的 Azure RemoteApp 云集合
 ![Azure RemoteApp - 使用 VNET 的云集合](./media/remoteapp-planvpn/ra-cloudvpn.png)

这表示 Azure RemoteApp 集合，其中 RemoteApp 会话主机需要访问的所有资源都部署在 Azure 中。 它们可位于与 RemoteApp VNET 相同的 VNET 中，或者位于 Azure 的不同 VNET 中。

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>使用 VNET 的 Azure RemoteApp 混合集合
![Azure RemoteApp - 使用 VNET 的混合集合](./media/remoteapp-planvpn/ra-hybridvpn.png)

这表示 Azure RemoteApp 集合，其中 RemoteApp 会话主机需要访问的一些资源部署在本地。 RemoteApp VNET 使用 Azure 混合技术（如点对点 VPN 或快速路由）链接到本地网络。

## <a name="how-the-system-works"></a>系统的工作方式
在底层，Azure RemoteApp 将 Azure 虚拟机（使用你上载的映像）部署到你在预配期间选择的虚拟网络子网。 如果你选择了混合集合，我们会尝试使用虚拟网络中提供的 DNS 服务器解析在预配工作流中输入的域控制器的 FQDN。  
如果要连接到现有的虚拟网络，请一定要公开 Azure RemoteApp 子网的网络安全组中的必需端口。 

我们建议你使用 [足够大的 Azure RemoteApp 子网](remoteapp-vnetsizing.md)。 Azure虚拟网络支持的最大值为 / 8（使用 CIDR 子网定义）。 当更多用户访问应用时，你的子网应该足够大以容纳所有 Azure RemoteApp VM。 

以下是需要在虚拟网络子网上启用的功能： 

1. 应允许来自子网的出站流量在端口范围 10101-10175 上与内部 Azure RemoteApp 服务之一进行通信。
2. 应允许子网中的出站流量连接到端口 443 上的 Azure 存储
3. 如果在 Azure 中托管 Active Directory，请确保 Azure RemoteApp 的虚拟网络子网中的任意 VM 都能够连接到此域控制器。 虚拟网络中的 DNS 应该能够解析此域控制器的 FQDN。

## <a name="virtual-network-with-forced-tunneling"></a>使用强制隧道的虚拟网络
[强制隧道](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) 现在支持所有新的 Azure RemoteApp 集合。 为了支持强制隧道，我们目前不支持迁移现有集合。  你必须使用链接到 Azure RemoteApp 的 VENT 删除所有现有集合并创建一个新集合，以在集合上启用强制隧道。 




<!--HONumber=Nov16_HO3-->


