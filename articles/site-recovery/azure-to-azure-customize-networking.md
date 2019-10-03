---
title: 为故障转移 VM 自定义网络配置 |Microsoft Docs
description: 概述在使用 Azure Site Recovery 复制 Azure VM 时，如何为故障转移 VM 自定义网络配置。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 1905d6afb023b1bed15f1359fed8477d815acb45
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087709"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>自定义目标 Azure VM 的网络配置

本文提供有关在使用 [Azure Site Recovery](site-recovery-overview.md) 将 Azure VM 从一个区域复制和恢复到另一个区域时，如何在目标 Azure VM 上自定义网络配置的指导。

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。

## <a name="supported-networking-resources"></a>支持的网络资源

复制 Azure VM 时，可为故障转移 VM 提供以下关键资源配置。

- [内部负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [公共 IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- 子网和 NIC 的[网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

 > [!IMPORTANT]
  > 这些设置仅在故障转移操作中受支持，而不适用于测试故障转移。

## <a name="pre-requisites"></a>先决条件

- 确保提前规划恢复端的配置。
- 需要提前创建网络资源。 请将此配置作为输入提供，使 Azure Site Recovery 服务能够遵守这些设置，并确保故障转移 VM 遵守这些设置。

## <a name="steps-to-customize-failover-networking-configurations"></a>自定义故障转移网络配置的步骤

1. 导航到“复制的项”。 
2. 单击所需的 Azure VM。
3. 依次单击“计算和网络”、“编辑”。 将会看到，NIC 配置设置包含源中的相应资源。 

     ![自定义](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 单击要配置的 NIC 旁边的“编辑”。 在接下来打开的边栏选项卡中，选择目标中已预先创建的相应资源。

    ![NIC-drilldown](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. 单击 **“确定”** 。

现在，Site Recovery 将会遵守这些设置，并确保故障转移 VM 通过相应的 NIC 连接到所选资源。

## <a name="troubleshooting"></a>疑难解答

### <a name="unable-to-view-or-select-a-resource"></a>无法查看或选择资源

如果你无法选择或查看某个网络资源，请根据以下条件执行相应的检查：

- 仅当源 VM 具有相应的输入时，才会启用网络资源的目标字段。 此行为基于以下原则：对于灾难恢复方案，应使用源的完全相同的版本或缩减版本。
- 对于每个相关网络资源，下拉列表中将应用一些筛选器，以确保故障转移 VM 可将自身附加到所选资源，并维持故障转移的可靠性。 这些筛选器基于配置源 VM 时已验证的相同网络条件。

内部负载均衡器验证：

1. LB 和目标 VM 的订阅与区域应该相同。
2. 目标 VM 的与内部负载均衡器关联的虚拟网络应该相同。
3. 目标 VM 的公共 IP SKU 和内部 Loadbalancer 的 SKU 应相同。
4. 如果将目标 VM 配置为放入可用性区域，请检查负载均衡器是区域冗余的还是任何可用性区域的一部分。 （基本 SKU 负载均衡器不支持区域，因此在这种情况下不会显示在下拉列表中。）
5. 确保内部负载均衡器上已预先创建后端池和前端配置。


公共 IP 地址：
    
1. 公共 IP 和目标 VM 的订阅与区域应该相同。
2. 目标 VM 的公共 IP SKU 和内部 Loadbalancer 的 SKU 应相同。

网络安全组：
1. 网络安全组和目标 VM 的订阅和区域应该相同。


> [!WARNING]
> 如果目标 VM 已关联到某个可用性集，则你需要将同一 SKU 的公共 IP/内部负载均衡器关联到可用性集中其他 VM 的公共 IP/内部负载均衡器。 否则可能导致故障转移失败。
