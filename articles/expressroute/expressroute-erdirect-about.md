---
title: 关于 ExpressRoute Direct - Azure | Microsoft Docs
description: 本页概述了 ExpressRoute Direct（预览版）
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 3124e98dd035080c2989849232b978c5d4a563ea
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100730"
---
# <a name="about-expressroute-direct-preview"></a>关于 ExpressRoute Direct（预览版）

使用 ExpressRoute Direct，可以直接连接到 Microsoft 战略性分布在全球的对等互连位置的的全球网络。 ExpressRoute Direct 提供双 100 Gbps 连接，支持大规模的主动/主动连接。

ExpressRoute Direct 提供的主要功能包括但不限于：

* 大规模数据引入到存储（如存储和 Cosmos DB）
* 针对受监管和需要专用和独立连接的行业的物理隔离，例如：银行、政府和零售
* 根据业务部门，细化控制线路分布

> [!IMPORTANT]
> ExpressRoute Direct 目前为预览版。
>
> 此公共预览版在提供时没有附带服务级别协议，不应该用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enroll-in-the-preview"></a>在预览版中注册

在使用 ExpressRoute Direct 之前，必须先在预览版中注册订阅。 若要注册，请使用你的订阅 ID 向 <ExpressRouteDirect@microsoft.com> 发送一封电子邮件，其中包括以下详细信息：

* 需通过 **ExpressRoute Direct** 完成的方案
* 位置首选项 - 请参阅[合作伙伴和对等互连位置](expressroute-locations-providers.md)，获取包含所有位置的完整列表
* 实现的时间线
* 其他问题

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>使用服务提供程序的 ExpressRoute，以及 ExpressRoute Direct

| **使用服务提供程序的 ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| 使用服务提供程序可以快速载入并连接到现有的基础结构 | 要求 100 Gbps 基础结构以及对所有层进行全面管理
| 集成数百个提供程序，包括以太网和 MPLS | 直接/专用容量，适用于受管制行业和大规模数据引入 |
| 从 50 Mbps 到 10 Gbps 的线路 SKU | 客户可以选择以下线路 SKU 的组合：5 Gbps、10 Gbps、40 Gbps、100 Gbps - 限制为总共 200 Gbps
| 针对单租户优化 | 针对单租户/云服务提供商/多个业务单位优化

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 线路

使用 Microsoft Azure ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 Dynamics 365 等 Microsoft 云服务建立连接。  

每个对等互连位置都可以访问 Microsoft 的全球网络，可以默认访问地缘政治区域中的任何区域，还可以访问使用高级线路的所有全球区域。  

大多数方案中的功能相当于使用 ExpressRoute 服务提供商来运营的线路。 若要支持进一步细分以及通过 ExpressRoute Direct 提供的新功能，可以使用 ExpressRoute Direct 线路上存在的某些关键功能。

## <a name="circuit-skus"></a>线路 SKU

ExpressRoute Direct 支持将数据大规模引入到 Azure 存储和其他大数据服务中的方案。 ExpressRoute Direct 上的 ExpressRoute 线路现在也支持 40 Gbps 和 100 Gbps 线路 SKU。 物理端口对仅为“100 Gbps”，可以有多个带宽为 5 Gbps、10 Gbps、40 Gbps、100 Gbps 的虚拟电路 - 任意组合最多可达 200 Gbps。 

## <a name="vlan-tagging"></a>VLAN 标记

ExpressRoute Direct 同时支持 QinQ 和 Dot1Q VLAN 标记。

* **QinQ VLAN 标记**允许基于单个 ExpressRoute 线路的隔离路由域。 Azure 在创建线路时动态分配一个不能更改的 S-Tag。 线路上的每个对等互连（专用对等互连和 Microsoft 对等互连）都会使用唯一的 C-Tag 作为 VLAN。 C-Tag 不需要在 ExpressRoute Direct 端口的所有线路上独一无二。

* **Dot1Q VLAN 标记**允许基于单个 ExpressRoute Direct 端口对的单个标记 VLAN。 在对等互连上使用的 C-Tag 必须在 ExpressRoute Direct 端口对的所有线路和对等互连中独一无二。

## <a name="workflow"></a>工作流

[ ![工作流](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct 提供相同的企业级 SLA，并且可以通过主动/主动冗余连接连接到 Microsoft 全球网络中。 ExpressRoute 基础设施是冗余的，连接到 Microsoft 全球网络的功能也是冗余的，并且具有多样性，可以根据客户需求进行相应的缩放。 预览版没有 SLA，只能用于非生产性工作负荷。

## <a name="next-steps"></a>后续步骤

[配置 ExpressRoute Direct](expressroute-howto-erdirect.md)
