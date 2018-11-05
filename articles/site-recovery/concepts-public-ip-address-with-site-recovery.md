---
title: 使用 Azure Site Recovery 进行故障转移后使用公共 IP 地址 | Microsoft Docs
description: 介绍如何结合使用 Azure Site Recovery 和 Azure 流量管理器设置公共 IP 地址来实现灾难恢复和迁移
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: mayg
ms.openlocfilehash: 80c38ecc766d60fba578e877998aeb216ea66012
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215270"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>设置故障转移后的公共 IP 地址

公共 IP 地址允许 Internet 资源与 Azure 资源进行入站通信。 在 IP 地址已分配给 Azure 资源的情况下，公共 IP 地址还允许这些资源与 Internet 和面向公众的 Azure 服务进行出站通信。
- 从 Internet 到资源的入站通信，如 Azure 虚拟机 (VM)、Azure 应用程序网关、Azure 负载均衡器、Azure VPN 网关等。 如果 VM 没有分配有公共 IP 地址，则仍可通过 Internet 与某些资源（如 VM）进行通信，前提是 VM 是负载均衡器后端池的一部分且负载均衡器分配有公共 IP 地址。
- 使用可预测的 IP 地址与 Internet 建立出站连接。 例如，如果某虚拟机未分配有公共 IP 地址，但其地址由 Azure 网络地址转换为可预测的公共地址，则默认情况下，该虚拟机可与 Internet 建立出站通信。 通过将公共 IP 地址分配给资源，可了解哪个 IP 地址用于出站连接。 尽管可预测，但地址可根据所选分配方法进行更改。 有关详细信息，请参阅[创建公共 IP 地址](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)。 有关从 Azure 资源建立出站连接的详细信息，请参阅[了解出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

在 Azure 资源管理器中，公共 IP 地址是具有其自身属性的资源。 可与公共 IP 地址资源关联的部分资源包括：

* 虚拟机网络接口
* 面向 Internet 的负载均衡器
* VPN 网关
* 应用程序网关数

本文介绍如何将公共 IP 地址与 Site Recovery 配合使用。

## <a name="public-ip-address-assignment-using-recovery-plan"></a>使用恢复计划分配公共 IP 地址

生产应用程序的公共 IP 地址不能在故障转移中保留。 作为故障转移过程的一部分启动的工作负载必须向其分配在目标区域可用的 Azure 公共 IP 资源。 此步骤可手动完成或使用恢复计划自动执行。 恢复计划将计算机汇集到恢复组中。 它可以帮助定义一个系统性恢复过程。 可以通过用于故障转移到 Azure 的 Azure 自动化 runbook 或通过脚本使用恢复计划来维持秩序，自动执行每个步骤所需的操作。

设置如下：
- 创建[恢复计划](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan)并根据需要将工作负载分组到计划中。
- 通过使用 [Azure 自动化 runbook](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan)脚本向故障转移 VM 添加附加公共 IP 地址的步骤来自定义计划。

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>使用 DNS 级别路由的公共终结点切换

Azure 流量管理器在终结点之间启用 DNS 级别路由，可帮助在灾难恢复方案中[降低 RTO](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations)。 

了解有关使用流量管理器的故障转移方案的详细信息：
1. 使用流量管理器的[本地到 Azure 故障转移](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) 
2. 使用流量管理器的 [Azure 到 Azure 故障转移](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) 

设置如下：
- 创建[流量管理器配置文件](../traffic-manager/traffic-manager-create-profile.md)。
- 利用“优先级”路由方法创建两个终结点 – 针对源的“主要”终结点，针对 Azure 的“故障转移”终结点。 为**主要**终结点分配优先级 1，为**故障转移**终结点分配优先级 2。
- “主要”终结点可以是 [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) 或[外部](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)终结点，具体取决于源环境在 Azure 内部还是外部。
- “故障转移”终结点被创建为“Azure”终结点。 使用静态公共 IP 地址，因为这将是灾难事件中流量管理器的面向外部的终结点。

## <a name="next-steps"></a>后续步骤
- 详细了解[将流量管理器与 Azure Site Recovery 配合使用](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- 详细了解流量管理器的[路由方法](../traffic-manager/traffic-manager-routing-methods.md)。
- 详细了解如何使用[恢复计划](site-recovery-create-recovery-plans.md)自动执行应用程序故障转移。
