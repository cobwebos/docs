---
title: 为故障转移 VM 自定义网络配置 |Microsoft Docs
description: 概述了在使用 Azure Site Recovery 复制 Azure VM 时，如何为故障转移 VM 自定义网络配置。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: sideeksh
ms.openlocfilehash: 599ad76f38833e06cc458fd0687921e6975e190b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426361"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>自定义目标 Azure VM 的网络配置

本文提供了有关在使用 [Azure Site Recovery](site-recovery-overview.md) 将 Azure 虚拟机 (VM) 从一个区域复制和恢复到另一个区域时，在目标 Azure VM 上自定义网络配置的指导。

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。

## <a name="supported-networking-resources"></a>支持的网络资源

复制 Azure VM 时，可以为故障转移 VM 提供以下关键资源配置：

- [内部负载均衡器](../load-balancer/load-balancer-overview.md)
- [公共 IP](../virtual-network/public-ip-addresses.md)
- 子网和 NIC 的[网络安全组](../virtual-network/manage-network-security-group.md)

## <a name="prerequisites"></a>必备条件

- 确保提前规划恢复端的配置。
- 提前创建网络资源。 请将此配置作为输入提供，使 Azure Site Recovery 服务能够遵守这些设置，并确保故障转移 VM 遵守这些设置。

## <a name="customize-failover-and-test-failover-networking-configurations"></a>自定义故障转移和测试故障转移网络配置

1. 转到“复制的项”。  
2. 选择所需的 Azure VM。
3. 选择“计算和网络”  ，然后选择“编辑”  。 注意，NIC 配置设置包含源中的相应资源。 

     ![自定义故障转移网络配置](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 选择一个测试故障转移虚拟网络。 你可以选择将其留空，并在测试故障转移时选择一个。
5. 选择要配置的 NIC 旁边的“编辑”。  在接下来打开的边栏选项卡中，在测试故障转移和故障转移位置中选择已预先创建的相应资源。

    ![编辑 NIC 配置](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. 选择“确定”  。

现在，Site Recovery 将会遵守这些设置，并确保故障转移 VM 通过相应的 NIC 连接到所选资源。

通过恢复计划触发测试故障转移时，它将始终请求 Azure 虚拟网络。 对于没有预先配置测试故障转移设置的虚拟机，此虚拟网络将用于测试故障转移。

## <a name="troubleshooting"></a>故障排除

### <a name="unable-to-view-or-select-a-resource"></a>无法查看或选择资源

如果你无法选择或查看某个网络资源，请根据以下条件执行相应的检查：

- 仅当源 VM 具有相应的输入时，才会启用网络资源的目标字段。 此行为基于以下原则：对于灾难恢复方案，应使用源的完全相同的版本或缩减版本。
- 对于每个网络资源，下拉列表中将应用一些筛选器，以确保故障转移 VM 可将自身附加到所选资源，并维持故障转移的可靠性。 这些筛选器基于配置源 VM 时已验证的相同网络条件。

内部负载均衡器验证：

- 负载均衡器与目标 VM 应当具有相同的订阅和区域。
- 与内部负载均衡器关联的虚拟网络应当与目标 VM 的虚拟网络相同。
- 目标 VM 的公共 IP SKU 和内部负载均衡器的 SKU 应相同。
- 如果将目标 VM 配置为放入可用性区域，请检查负载均衡器是区域冗余的还是任何可用性区域的一部分。 （基本 SKU 负载均衡器不支持区域，因此在这种情况下不会显示在下拉列表中。）
- 请确保内部负载均衡器上具有已预先创建的后端池和前端配置。

公共 IP 地址：

- 公共 IP 与目标 VM 的订阅和区域应当相同。
- 目标 VM 的公共 IP SKU 和内部负载均衡器的 SKU 应相同。

网络安全组：
- 网络安全组与目标 VM 的订阅和区域应当相同。


> [!WARNING]
> 如果目标 VM 与某个可用性集相关联，则你需要将同一 SKU 的公共 IP 和内部负载均衡器关联到可用性集中另一 VM 的公共 IP 和内部负载均衡器。 否则，故障转移可能不会成功。
