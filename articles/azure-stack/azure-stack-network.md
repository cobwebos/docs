---
title: "网络集成的 Azure 堆栈系统的集成注意事项 |Microsoft 文档"
description: "了解可以执行哪些操作来规划与多节点 Azure 堆栈的数据中心网络集成。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 04cfe3c4ac6011b9c3d31b7d4ac3c018c350d67b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="network-connectivity"></a>网络连接
本文提供了 Azure 堆栈网络基础结构信息来帮助你决定如何最好地将 Azure 堆栈集成到你现有的网络环境。 

> [!NOTE]
> 若要解决从 Azure 堆栈 (例如，www.bing.com) 的外部 DNS 名称，你需要提供 DNS 服务器将 DNS 请求转发。 有关 Azure 堆栈 DNS 要求的详细信息，请参阅[Azure 堆栈数据中心集成的 DNS](azure-stack-integrate-dns.md)。

## <a name="physical-network-design"></a>物理网络设计
Azure 堆栈解决方案需要弹性和高可用物理基础结构以支持其操作和服务。 下图表示我们推荐的设计：

![推荐的 Azure 堆栈网络设计](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>逻辑网络
逻辑网络表示基础物理网络基础结构的抽象。 它们用于组织和简化针对主机、 虚拟机和服务的网络分配。 作为逻辑网络创建的一部分，可以创建网络站点定义的虚拟局域网 (Vlan)、 IP 子网和 IP 子网 /VLAN 对与每个物理位置中的逻辑网络关联。

下表显示了逻辑网络和关联的 IPv4 子网地址范围，必须考虑的：

| 本地网络 | 说明 | 大小 | 
| -------- | ------------- | ------------ | 
| 公共 VIP | Azure 堆栈服务，由租户虚拟机使用的其余部分一小部分的公共 IP 地址。 Azure 堆栈基础结构使用此网络中的 32 个地址。 如果你打算使用 App Service 和 SQL 资源提供程序，将使用 7 个地址。 | 月 26 日 （62 主机）-/ 22 （1022年主机）<br><br>建议 = / 24 （254 个主机） | 
| 交换机基础结构 | 路由的目的，专用的点到点 IP 地址切换管理界面和环回地址分配给交换机。 | /26 | 
| 基础结构 | 用于 Azure 堆栈内部组件进行通信。 | /24 |
| 专用 | 用于存储网络和私有 Vip。 | /24 | 
| BMC | 用于与在物理主机上的 Bmc 进行通信。 | /27 | 
| | | |

## <a name="network-infrastructure"></a>网络基础结构
Azure 堆栈的网络基础结构包含的交换机配置的多个逻辑网络。 下图显示这些逻辑网络，以及它们与 top-柜顶式 (TOR)，集成基板管理控制器 (BMC) 和边框 （客户网络） 交换机。

![逻辑网络关系图和交换机连接](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC 网络
到管理网络情况下，此网络专用于连接的所有基板管理控制器 （也称为服务处理器，例如，iDRAC、 iLO、 iBMC 等）。 如果存在，硬件生命周期主机 (HLH) 位于此网络上，可以提供 OEM 特定软件的硬件维护或监视。 

HLH 还承载部署 VM (DVM)。 DVM Azure 堆栈部署过程中使用，并且部署完成后被删除。 DVM 需要 internet 连接的部署方案，以便测试、 验证和访问多个组件中的访问权限。 这些组件可以是内部和外部企业网络;例如 NTP、 DNS 和 Azure。 有关连接要求的详细信息，请参阅[Azure 堆栈防火墙集成中的 NAT 部分](azure-stack-firewall.md#network-address-translation)。 

### <a name="private-network"></a>专用网络
(254 主机 IP 的) 网络是专用的到 Azure 堆栈区域 （也不会扩展超出 Azure 堆栈区域的边框交换机设备），而且划分为两个子网此/24:

- **存储网络**。 用于支持使用空间直通和服务器消息块 (SMB) 存储通信和虚拟机 (126 主机 IP 的) 网络 / 25 实时迁移。 
- **内部虚拟 IP 网络**。 A/25 网络软件负载平衡器的专用于仅限内部使用的 Vip。

### <a name="azure-stack-infrastructure-network"></a>Azure 堆栈的基础结构网络
这/24 网络专用于内部 Azure 堆栈组件，以便他们可以进行通信和交换彼此之间的数据。 此子网需要可路由的 IP 地址，但将保持私有到解决方案通过使用访问控制列表 (Acl)。 它不应超出除外 / 27 大小等于较小范围的边框交换机路由网络利用其中的某些服务，它们需要外部资源和/或 internet 访问时。 

### <a name="public-infrastructure-network"></a>公共基础结构网络
这/27 网络是从前面所述的 Azure 堆栈基础结构子网较小的范围，它不需要公共 IP 地址，但它需要通过 NAT 或透明的代理访问 internet。 此网络将分配的紧急情况下恢复控制台系统 (ERCS)，ERCS VM 注册到 Azure 期间需要访问 internet，并且应该路由到你的管理网络进行故障排除。

### <a name="public-vip-network"></a>公共 VIP 网络
公共 VIP 网络分配给 Azure 堆栈中的网络控制器。 它不是在交换机上的逻辑网络。 SLB 使用的地址池，并将分配/32 网络的租户工作负载。 交换机路由表，这些 32 Ip 正为可通过 BGP 路由播发。 此网络包含外部访问或公共 IP 地址。 Azure 堆栈基础结构使用从此公共 VIP 网络至少 8 个地址，而其余部分由租户虚拟机。 此子网的网络大小的范围可以从最小/26 （64 个主机） 到最大/22 （1022年主机），我们建议你计划对于/24 网络。

### <a name="switch-infrastructure-network"></a>交换机基础结构网络
这/26 网络是包含可路由点对点 IP/30 (2 主机 IP 的) 子网和环回的专用子网/32 个带内的子网切换管理和 BGP 路由器 id。 此范围内的 IP 地址必须是外部的数据中心的 Azure 堆栈解决方案可路由，它们可能私有或公共 Ip。

### <a name="switch-management-network"></a>交换机管理网络
此/29 （6 主机 Ip） 网络专用于连接的开关的管理端口。 它允许部署、 管理和故障排除的带外访问权限。 它是从上面提到的交换机基础结构网络中进行计算。

## <a name="publish-azure-stack-services"></a>发布 Azure 堆栈服务
你将需要 Azure 堆栈服务向用户提供从外部 Azure 堆栈。 Azure 堆栈将设置其基础结构角色的各种终结点。 这些终结点从公共 IP 地址池分配 Vip。 为每个终结点在部署时指定的外部 DNS 区域中创建 DNS 条目。 例如，用户门户分配门户的 DNS 主机的条目。*&lt;区域 >。&lt;fqdn >*。

### <a name="ports-and-urls"></a>端口和 Url
若要使 Azure 堆栈的服务 （如门户，Azure 资源管理器、 DNS 等） 可用的外部网络，则必须允许与这些终结点的入站的流量特定 Url、 端口和协议。
 
在部署中其中透明代理的上行以传统的代理服务器，则必须允许特定端口和 Url 同时[入站](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)和[出站](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)通信。 这些包括端口和 Url 标识、 应用商店联合、 修补程序和更新、 注册和使用情况数据。

## <a name="next-steps"></a>后续步骤
[边框连接](azure-stack-border-connectivity.md)