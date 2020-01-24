---
title: 为故障转移 VM 自定义网络配置 |Microsoft Docs
description: 提供有关使用 Azure Site Recovery 在 Azure Vm 的复制中为故障转移 VM 自定义网络配置的概述。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292852"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>自定义目标 Azure VM 的网络配置

本文提供了有关在使用[Azure Site Recovery](site-recovery-overview.md)将 azure vm 从一个区域复制和恢复到另一个区域时，如何在目标 Azure 虚拟机（VM）上自定义网络配置的指导。

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。

## <a name="supported-networking-resources"></a>支持的网络资源

可在复制 Azure Vm 时为故障转移 VM 提供以下关键资源配置：

- [内部负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [公共 IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- 子网和 NIC 的[网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

## <a name="prerequisites"></a>必备组件

- 请确保提前计划恢复端配置。
- 提前创建网络资源。 提供它作为输入，以便 Azure Site Recovery 服务可以服从这些设置，并确保故障转移 VM 符合这些设置。

## <a name="customize-failover-and-test-failover-networking-configurations"></a>自定义故障转移和测试故障转移网络配置

1. 中转到 "**复制的项**"。 
2. 选择所需的 Azure VM。
3. 选择 "**计算和网络**"，然后选择 "**编辑**"。 请注意，NIC 配置设置包含源中的相应资源。 

     ![自定义故障转移网络配置](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 选择 "测试故障转移" 虚拟网络。 你可以选择将其留空，并在测试故障转移时选择一个。
5. 故障转移网络在要配置的 NIC 附近选择 "**编辑**"。 在打开的下一个边栏选项卡中，选择 "测试故障转移和故障转移位置" 中的相应预创建资源。

    ![编辑 NIC 配置](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. 选择“确定”。

Site Recovery 现在将遵循这些设置，并确保故障转移上的 VM 通过相应的 NIC 连接到所选资源。

通过恢复计划触发测试故障转移时，它将始终询问 Azure 虚拟网络。 此虚拟网络将用于预配置测试故障转移设置的计算机的测试故障转移。

## <a name="troubleshooting"></a>故障排除

### <a name="unable-to-view-or-select-a-resource"></a>无法查看或选择资源

如果您无法选择或查看网络资源，请完成以下检查和条件：

- 仅当源 VM 具有相应的输入时，才会启用网络资源的目标字段。 这基于灾难恢复方案的原则，你需要的是源的确切或缩小版本。
- 对于每个网络资源，会在下拉列表中应用一些筛选器，以确保故障转移 VM 可以将自身附加到所选资源并维护故障转移的可靠性。 这些筛选器基于在配置源 VM 时已验证的相同网络条件。

内部负载均衡器验证：

- 负载均衡器和目标 VM 的订阅和区域应相同。
- 与内部负载均衡器关联的虚拟网络与目标 VM 的虚拟网络应相同。
- 目标 VM 的公共 IP SKU 和内部负载均衡器的 SKU 应相同。
- 如果将目标 VM 配置为放置在可用性区域中，则检查负载均衡器是区域冗余还是任何可用性区域的一部分。 （基本 SKU 负载均衡器不支持区域，并且在此情况下不会显示在下拉列表中。）
- 确保内部负载均衡器具有预先创建的后端池和前端配置。

公共 IP 地址：

- 公共 IP 和目标 VM 的订阅和区域应相同。
- 目标 VM 的公共 IP SKU 和内部负载均衡器的 SKU 应相同。

网络安全组：
- 网络安全组和目标 VM 的订阅和区域应相同。


> [!WARNING]
> 如果目标 VM 与某个可用性集相关联，则需要将同一 SKU 的公共 IP 和内部负载均衡器与另一个 VM 的公共 IP 和可用性集中的内部负载均衡器的内部负载均衡器相关联。 否则，故障转移可能不会成功。
