---
title: 关于 ExpressRoute Direct - Azure | Microsoft Docs
description: 此页提供 ExpressRoute 直通的概述
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 916175401cd993392094b2bb16f8fc8746a4d2a8
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123399"
---
# <a name="about-expressroute-direct"></a>关于 ExpressRoute Direct

使用 ExpressRoute Direct，可以直接连接到 Microsoft 战略性分布在全球的对等互连位置的的全球网络。 ExpressRoute Direct 提供双 100 Gbps 或 10 Gbps 连接，支持大规模主动/主动连接。

ExpressRoute Direct 提供的主要功能包括但不限于：

* 大规模数据引入到存储（如存储和 Cosmos DB）
* 针对受监管和需要专用和独立连接的行业的物理隔离，例如：银行、政府和零售
* 根据业务部门，细化控制线路分布

## <a name="onboard-to-expressroute-direct"></a>集成到 ExpressRoute 直接

使用 ExpressRoute Direct 之前，必须先注册订阅。 若要注册，请使用你的订阅 ID 向 <ExpressRouteDirect@microsoft.com> 发送一封电子邮件，其中包括以下详细信息：

* 需通过 **ExpressRoute Direct** 完成的方案
* 位置首选项 - 请参阅[合作伙伴和对等互连位置](expressroute-locations-providers.md)，获取包含所有位置的完整列表
* 实现的时间线
* 其他问题

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>使用服务提供程序的 ExpressRoute，以及 ExpressRoute Direct

| **使用服务提供程序的 ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| 使用服务提供程序可以快速载入并连接到现有的基础结构 | 需要 100 Gbps/10 Gbps 基础结构和所有层的完全管理
| 集成数百个提供程序，包括以太网和 MPLS | 直接/专用容量，适用于受管制行业和大规模数据引入 |
| 从 50 Mbps 到 10 Gbps 的线路 SKU | 客户可以在 100 Gbps ExpressRoute 直接上选择以下线路 Sku 的组合： <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> 客户可在 10 Gbps ExpressRoute 直接上选择以下线路 Sku 的组合：<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| 针对单租户优化 | 针对具有多个业务单位和多个工作环境的单个租户进行了优化

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 线路

使用 Microsoft Azure ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 通过 ExpressRoute，你可以与 Microsoft 云服务（如 Microsoft Azure 和 Office 365）建立连接。

每个对等互连位置都可以访问 Microsoft 的全球网络，可以默认访问地缘政治区域中的任何区域，还可以访问使用高级线路的所有全球区域。  

大多数方案中的功能相当于使用 ExpressRoute 服务提供商来运营的线路。 若要支持进一步细分以及通过 ExpressRoute Direct 提供的新功能，可以使用 ExpressRoute Direct 线路上存在的某些关键功能。

## <a name="circuit-skus"></a>线路 SKU

ExpressRoute Direct 支持将数据大规模引入到 Azure 存储和其他大数据服务中的方案。 100 Gbps ExpressRoute 直通上的 ExpressRoute 线路现在还支持**40 gbps**和**100 Gbps 的 gbps**电路 sku。 物理端口对只为**100 或 10 Gbps** ，可以有多个虚拟线路。 线路大小：

| **100 Gbps ExpressRoute 直接** | **10 Gbps ExpressRoute 直接** | 
| --- | --- |
| **订阅的带宽**：200 Gbps | **订阅的带宽**：20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>技术要求

* Microsoft 企业边缘路由器（MSEE）接口：
    * 仅限跨路由器对的双10或100千兆以太网端口
    * 单模式 LR 光纤连接
    * IPv4 和 IPv6
    * IP MTU 1500 字节

* 交换机/路由器第2层/第3层连接：
    * 必须支持 1 802.1 Q （Dot1Q）标记或两个标记 802.1 Q （QinQ）标记封装
    * Ethertype = 0x8100
    * 必须基于 Microsoft 指定的 VLAN ID （*仅适用于 QinQ* ）添加外部 VLAN 标记（STAG）。
    * 必须支持每个端口和设备多个 BGP 会话（Vlan）
    * IPv4 和 IPv6 连接。 *对于 IPv6，将不会创建其他子接口。IPv6 地址将添加到现有子接口*。 
    * 可选：[双向转发检测（BFD）](https://docs.microsoft.com/azure/expressroute/expressroute-bfd)支持，默认情况下在 ExpressRoute 线路上的所有专用对等互连上配置

## <a name="vlan-tagging"></a>VLAN 标记

ExpressRoute Direct 同时支持 QinQ 和 Dot1Q VLAN 标记。

* **QinQ VLAN 标记**允许基于单个 ExpressRoute 线路的隔离路由域。 Azure 在创建线路时动态分配一个不能更改的 S-Tag。 线路上的每个对等互连（专用对等互连和 Microsoft 对等互连）都会使用唯一的 C-Tag 作为 VLAN。 C-Tag 不需要在 ExpressRoute Direct 端口的所有线路上独一无二。

* **Dot1Q VLAN 标记**允许基于单个 ExpressRoute Direct 端口对的单个标记 VLAN。 在对等互连上使用的 C-Tag 必须在 ExpressRoute Direct 端口对的所有线路和对等互连中独一无二。

## <a name="workflow"></a>工作流

[![workflow](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct 提供相同的企业级 SLA，并且可以通过主动/主动冗余连接连接到 Microsoft 全球网络中。 ExpressRoute 基础设施是冗余的，连接到 Microsoft 全球网络的功能也是冗余的，并且具有多样性，可以根据客户需求进行相应的缩放。 

## <a name="next-steps"></a>后续步骤

[配置 ExpressRoute Direct](expressroute-howto-erdirect.md)
