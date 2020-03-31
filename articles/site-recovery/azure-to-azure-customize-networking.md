---
title: 为故障转移 VM 自定义网络配置 |微软文档
description: 提供使用 Azure 站点恢复复制 Azure VM 时故障转移 VM 的自定义网络配置概述。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292852"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>自定义目标 Azure VM 的网络配置

本文提供有关使用[Azure 站点恢复](site-recovery-overview.md)将 Azure VM 从一个区域复制和恢复 Azure VM 时在目标 Azure 虚拟机 （VM） 上自定义网络配置的指导。

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。

## <a name="supported-networking-resources"></a>支持的网络资源

在复制 Azure VM 时，可以为故障转移 VM 提供以下关键资源配置：

- [内部负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [公共知识产权](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- 子网和 NIC 的[网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

## <a name="prerequisites"></a>先决条件

- 确保提前规划恢复端的配置。
- 提前创建网络资源。 请将此配置作为输入提供，使 Azure Site Recovery 服务能够遵守这些设置，并确保故障转移 VM 遵守这些设置。

## <a name="customize-failover-and-test-failover-networking-configurations"></a>自定义故障转移和测试故障转移网络配置

1. 转到**复制项**。 
2. 选择所需的 Azure VM。
3. 选择 **"计算和网络"** 并选择 **"编辑**"。 请注意，NIC 配置设置在源处包含相应的资源。 

     ![自定义故障转移网络配置](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 选择测试故障转移虚拟网络。 您可以选择将其留空，并在测试故障转移时选择一个。
5. 故障转移网络是在要配置的 NIC 附近选择**编辑**。 在打开的下一个边栏选项卡中，在测试故障转移和故障转移位置中选择相应的预创建资源。

    ![编辑 NIC 配置](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. 选择“确定”。

现在，Site Recovery 将会遵守这些设置，并确保故障转移 VM 通过相应的 NIC 连接到所选资源。

通过恢复计划触发测试故障转移时，它将始终询问 Azure 虚拟网络。 此虚拟网络将用于未预配置测试故障转移设置的计算机的测试故障转移。

## <a name="troubleshooting"></a>疑难解答

### <a name="unable-to-view-or-select-a-resource"></a>无法查看或选择资源

如果无法选择或查看网络资源，请通过以下检查和条件：

- 仅当源 VM 具有相应的输入时，才启用网络资源的目标字段。 这基于以下原则：对于灾难恢复方案，您需要源的精确版本或缩减版本。
- 对于每个网络资源，在下拉列表中应用一些筛选器，以确保故障转移 VM 可以将自己附加到所选资源并保持故障转移可靠性。 这些筛选器基于配置源 VM 时已验证的相同网络条件。

内部负载均衡器验证：

- 负载均衡器和目标 VM 的订阅和区域应相同。
- 与内部负载均衡器和目标 VM 关联的虚拟网络应相同。
- 目标 VM 的公共 IP SKU 和内部负载均衡器的 SKU 应相同。
- 如果将目标 VM 配置为放入可用性区域，请检查负载均衡器是区域冗余的还是任何可用性区域的一部分。 （基本 SKU 负载均衡器不支持区域，在这种情况下不会显示在下拉列表中。
- 确保内部负载均衡器具有预先创建的后端池和前端配置。

公共 IP 地址：

- 公共 IP 和目标 VM 的订阅和区域应相同。
- 目标 VM 的公共 IP SKU 和内部负载均衡器的 SKU 应相同。

网络安全组：
- 网络安全组和目标 VM 的订阅和区域应相同。


> [!WARNING]
> 如果目标 VM 与可用性集相关联，则需要将同一 SKU 的公共 IP 和内部负载均衡器与其他 VM 的公共 IP 和可用性集中的内部负载均衡器关联。 如果没有，故障转移可能不会成功。
