---
title: Azure Stack 集成系统的网络集成注意事项 | Microsoft Docs
description: 了解可以执行哪些操作来规划数据中心网络与多节点 Azure Stack 的集成。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 2d16d1dc7a53ca388b00ba02b6447e178a9f6edb
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989231"
---
# <a name="network-connectivity"></a>网络连接
本文提供 Azure Stack 网络基础架构信息，可帮助你确定如何以最佳方式将 Azure Stack 集成到现有的网络环境。 

> [!NOTE]
> 若要从 Azure Stack 解析外部 DNS 名称（例如 www.bing.com），必须提供 DNS 服务器来转发 DNS 请求。 有关 Azure Stack DNS 要求的详细信息，请参阅 [Azure Stack 数据中心集成 - DNS](azure-stack-integrate-dns.md)。

## <a name="physical-network-design"></a>物理网络设计
Azure Stack 解决方案需有弹性且高度可用的物理基础结构才能支持其操作和服务。 从 ToR 到边界交换机的上行仅限于 SFP + 媒体和 1 GB 或 10 GB 的速度。 请与原始设备制造商 (OEM) 硬件供应商的可用性。 下图显示了建议的设计：

![建议的 Azure Stack 网络设计](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>逻辑网络
逻辑网络表示底层物理网络基础结构的抽象。 它们用于组织和简化主机、虚拟机和服务的网络分配。 网络站点在创建逻辑网络时创建，定义虚拟局域网 (VLAN)、IP 子网，以及与每个物理位置中逻辑网络关联的 IP 子网/VLAN 对。

下表显示了逻辑网络以及必须规划的关联 IPv4 子网范围：

| 本地网络 | 说明 | 大小 | 
| -------- | ------------- | ------------ | 
| 公共 VIP | Azure Stack 总共使用此网络中的 32 个地址。 8 个公共 IP 地址由少量的 Azure Stack 服务使用，剩余的地址由租户虚拟机使用。 如果打算使用应用服务和 SQL 资源提供程序，则还要额外使用 7 个地址。 | /26（62 台主机）- /22（1022 台主机）<br><br>建议使用 /24（254 台主机） | 
| 交换机基础结构 | 用于路由的专用交换机管理接口的点到点 IP 地址，以及分配给交换机的环回地址。 | /26 | 
| 基础结构 | 用于通信的 Azure Stack 内部组件。 | /24 |
| 专用 | 用于存储网络和专用 VIP。 | /24 | 
| BMC | 用于与物理主机上的 BMC 通信。 | /27 | 
| | | |

## <a name="network-infrastructure"></a>网络基础结构
Azure Stack 的网络基础结构包括交换机上配置的多个逻辑网络。 下图显示了这些逻辑网络，及其如何与架顶 (TOR)、基板管理控制器 (BMC) 和边界（客户网络）交换机集成。

![逻辑网络示意图和交换机连接](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC 网络
此网络专门用于将所有基板管理控制器（也称为服务处理器，例如 iDRAC、iLO、iBMC 等）连接到管理网络。 如果有，硬件生命周期主机 (HLH) 位于此网络上，并可提供 OEM 特定的软件用于硬件维护或监视。 

HLH 也托管部署 VM (DVM)。 此 DVM 在 Azure Stack 部署期间使用，在部署完成后删除。 此 DVM 在联网部署场景中需要进行 Internet 访问，以便测试、验证和访问多个组件。 这些组件可以是内部和外部企业网络;例如，NTP、 DNS 和 Azure。 有关连接要求的详细信息，请参阅 [Azure Stack 防火墙集成中的 NAT 部分](azure-stack-firewall.md#network-address-translation)。 

### <a name="private-network"></a>专用网络
此/24 （254 个主机 Ip） 网络专用于 Azure Stack 区域 （不会扩展 Azure Stack 区域的边界交换机设备以外），并划分成两个子网：

- **存储网络**。 / 25 （126 个主机 Ip） 网络用于支持使用空间直通和服务器消息块 (SMB) 存储流量和虚拟机实时迁移。 
- **内部虚拟 IP 网络**。 一个 /25 网络，专用于软件负载均衡器的仅限内部的 VIP。

### <a name="azure-stack-infrastructure-network"></a>Azure Stack 基础结构网络
此 /24 网络专用于内部 Azure Stack 组件，使这些组件能够相互通信和交换数据。 此子网需要可路由的 IP 地址，但使用访问控制列表 (ACL) 可将它保留给解决方案专用。 此网络在路由时，不应超出边界交换机，但大小等同于 /27 网络的小范围除外，其中一些服务在需要访问外部资源和/或 Internet 时使用 /27 网络。 

### <a name="public-infrastructure-network"></a>公共基础结构网络
此 /27 网络属于前面所述 Azure Stack 基础结构子网的较小范围，它无需公共 IP 地址，但确实需要通过 NAT 或透明代理进行 Internet 访问。 此网络将分配给紧急恢复控制台系统 (ERCS)。ERCS VM 在注册到 Azure 期间以及进行基础结构备份期间需要访问 Internet，。 ERCS VM 应可路由到管理网络，以便进行故障排除。

### <a name="public-vip-network"></a>公共 VIP 网络
公共 VIP 网络分配给 Azure Stack 中的网络控制器。 它不是交换机上的逻辑网络。 SLB 针对租户工作负荷使用地址池并分配 /32 网络。 在交换机路由表中，这些 /32 IP 通过 BGP 播发为可用路由。 此网络包含外部可访问的 IP 地址或公共 IP 地址。 Azure Stack 基础结构会保留此公共 VIP 网络中的前 31 个地址，剩余的地址由租户 VM 使用。 此子网中的网络大小范围为最小 /26（64 台主机）到最大 /22（1022 台主机），我们建议规划 /24 网络。

### <a name="switch-infrastructure-network"></a>交换机基础结构网络
此/26 网络是包含可路由的点到点 IP/30 （2 个主机 Ip） 子网和环回，专用子网/32 子网的带内交换机管理和 BGP 路由器 id。 此 IP 地址范围必须可从 Azure Stack 解决方案外部路由到数据中心，可以是专用或公共 IP。

### <a name="switch-management-network"></a>交换机管理网络
此 /29（6 个主机 IP）网络专用于连接交换机的管理端口。 其允许带外访问，以完成部署、管理和故障排除。 它是从上述交换机基础结构网络计算而来的。

## <a name="publish-azure-stack-services"></a>发布 Azure Stack 服务
需将 Azure Stack 服务提供给 Azure Stack 外部的用户使用。 Azure Stack 将为其基础结构角色设置不同的终结点。 这些终结点是公共 IP 地址池中分配的 VIP。 将会根据部署时的指定，针对外部 DNS 区域中的每个终结点创建一个 DNS 条目。 例如，将为用户门户分配 DNS 主机条目 portal.*&lt;region>.&lt;fqdn>*。

### <a name="ports-and-urls"></a>端口和 URL
若要使 Azure Stack 服务（例如门户、Azure 资源管理器、DNS 等）可供外部网络使用，必须允许特定 URL、端口和协议的入站流量发往这些终结点。
 
如果部署中的透明代理上行链接到传统的代理服务器，则必须允许特定的端口和 URL，以便能够进行[入站](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)和[出站](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)通信。 这包括用于标识、市场联合、修补和更新、注册和用量数据的端口与 URL。

## <a name="next-steps"></a>后续步骤
[边界连接](azure-stack-border-connectivity.md)