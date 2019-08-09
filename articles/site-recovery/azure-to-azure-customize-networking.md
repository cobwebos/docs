---
title: 为故障转移 VM 自定义网络配置 |Microsoft Docs
description: 提供有关使用 Azure Site Recovery 在 Azure Vm 的复制中为故障转移 VM 自定义网络配置的概述。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 907a698a675a039dfdc852210adecb94c7bfab25
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886892"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>自定义目标 Azure VM 的网络配置

本文提供了有关在使用[Azure Site Recovery](site-recovery-overview.md)将 azure vm 从一个区域复制和恢复到另一个区域时, 如何在目标 Azure VM 上自定义网络配置的指导。

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。

## <a name="support-networking-resources"></a>支持网络资源

可在复制 Azure Vm 时为故障转移 VM 提供以下关键资源配置。

- [内部负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [公共 IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- 子网和 NIC 的[网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

## <a name="pre-requisites"></a>先决条件

- 请确保提前计划恢复端配置。
- 需要预先创建网络资源。 提供它作为输入, 以便 Azure Site Recovery 服务可以服从这些设置, 并确保故障转移 VM 符合这些设置。

## <a name="steps-to-customize-failover-networking-configurations"></a>自定义故障转移网络配置的步骤

1. 导航到 "**复制的项**"。 
2. 单击所需的 Azure VM。
3. 单击 "**计算和网络**", 然后单击 "**编辑**"。 你将注意到 NIC 配置设置包含源中的相应资源。 

     ![自定义](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 单击要配置的 NIC 附近的 "**编辑**"。 在打开的下一个边栏选项卡中, 选择目标中的相应预创建资源。

    ![NIC-深化](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. 单击 **“确定”** 。

Site Recovery 现在将遵循这些设置, 并确保故障转移上的 VM 通过相应的 NIC 连接到所选资源。

## <a name="troubleshooting"></a>疑难解答

### <a name="unable-to-view-or-select-a-resource"></a>无法查看或选择资源

如果您无法选择或查看网络资源, 请执行以下检查 & 条件:

- 仅当源 VM 具有相应的输入时, 才会启用网络资源的目标字段。 这取决于灾难恢复方案的原则, 你需要源的确切或缩减版本。
- 对于每个问题的网络资源, 会在下拉列表中应用某些筛选器, 以确保故障转移 VM 可以将自身附加到所选资源并维护故障转移的可靠性。 这些筛选器基于在配置源 VM 时已验证的相同网络条件。

内部负载均衡器验证:

1. LB 和目标 VM 的订阅和区域应相同。
2. 与内部负载均衡器关联的虚拟网络与目标 VMshould 的虚拟网络相同。
3. 目标 VM 的公共 IP SKU 和内部 Loadbalancer 的 SKU 应相同。
4. 如果将目标 VM 配置为放置在可用性区域中, 则检查负载均衡器是区域冗余还是任何可用性区域的一部分。 (基本 SKU 负载均衡器不支持区域, 这种情况下, 将不会显示在下拉端中。)
5. 确保内部 LoadBalancer 具有预先创建的后端池和前端配置。


公共 IP 地址:
    
1. 公共 IP 和目标 VM 的订阅和区域应该是相同的。
2. 目标 VM 的公共 IP SKU 和内部 Loadbalancer 的 SKU 应相同。

网络安全组:
1. 网络安全组和目标 VM 的订阅和区域应相同。


> [!WARNING]
> 如果目标 VM 关联到某个可用性集, 则需要将同一 SKU 的公共 IP/内部负载均衡器与可用性集中其他 VM 的公共 IP/内部负载均衡器的关联。 如果不这样做, 可能会导致故障转移失败。
