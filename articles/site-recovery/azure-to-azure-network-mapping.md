---
title: 在 Azure Site Recovery 中的两个 Azure 区域之间映射虚拟网络 | Microsoft Docs
description: Azure Site Recovery 可以协调虚拟机和物理服务器的复制、故障转移与恢复。 了解有关故障转移到 Azure 或辅助数据中心的信息。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: mayg
ms.openlocfilehash: 99c7309e22d8ebe61a0a85b38c92bd3027977848
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013127"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>设置 VNet 的网络映射和 IP 寻址

本文介绍如何映射不同 Azure 区域中的两个 Azure 虚拟网络 (VNet) 实例，以及如何设置网络之间的 IP 寻址。 网络映射提供了用于在启用复制的时间基于源网络的目标网络选择的默认行为。

## <a name="prerequisites"></a>必备组件

在映射网络之前，应在源和目标 Azure 区域中创建 [Azure VNet](../virtual-network/virtual-networks-overview.md)。 

## <a name="set-up-network-mapping-manually-optional"></a>设置网络映射手动 （可选）

按如下所述映射网络：

1. 在“Site Recovery 基础结构”中，单击“+网络映射”。

    ![ 创建网络映射](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. 在“添加网络映射”中，选择源和目标位置。 在本示例中，源 VM 在“东亚”区域运行，将复制到“东南亚”区域。

    ![选择源和目标](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. 现在，在对方目录中创建网络映射。 在本示例中，源现在是“东南亚”，目标是“东亚”。

    ![添加网络映射窗格 - 选择目标网络的源和目标位置](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>启用复制时映射网络

如果在为 Azure VM 配置灾难恢复之前尚未准备好网络映射，可以在[设置和启用复制](azure-to-azure-how-to-enable-replication.md)时指定目标网络。 执行此操作时会发生以下情况：

- Site Recovery 根据选择的目标，自动创建从源到目标区域以及从目标到源区域的网络映射。
- 默认情况下，Site Recovery 会在目标区域中创建与源网络相同的网络。 Site Recovery 将 **-asr** 作为后缀添加到源网络名称。 可以自定义目标网络。
- 如果已经存在到源网络的网络映射，映射的目标网络时启用更多虚拟机的复制将始终为默认值。 您可以选择通过从下拉列表中选择其他可用选项来更改目标虚拟网络。 
- 若要更改新复制的默认目标虚拟网络，你需要修改现有的网络映射。
- 如果你想要修改从区域 A 到区域 B 的网络映射，请确保你首先删除网络映射从区域 B 到区域 a。反向映射删除后，修改从区域 A 到区域 B 的网络映射，然后创建相关的反向映射。

>[!NOTE]
>* 修改网络映射只会更改新的 VM 复制的默认值。 它不会影响现有的复制的目标虚拟网络选择。 
>* 如果你想要修改目标网络中的现有复制，请转到计算和网络设置的复制的项。

## <a name="specify-a-subnet"></a>指定子网

根据源 VM 子网的名称选择目标 VM 的子网。

- 如果可在目标网络中找到与源 VM 子网同名的子网，则为目标 VM 设置该子网。
- 如果目标网络中没有同名的子网，则按字母顺序设置第一个子网作为目标子网。
- 可以在 VM 的“计算和网络”设置中修改此设置。

    ![计算和网络计算属性窗口](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>设置目标 VM 的 IP 寻址

目标虚拟机上每个 NIC 的 IP 地址配置如下：

- **DHCP**：如果源 VM 的 NIC 使用 DHCP，则目标 VM 的 NIC 也设置为使用 DHCP。
- **静态 IP 地址**：如果源 VM 的 NIC 使用静态 IP 地址，则目标 VM NIC 也使用静态 IP 地址。


## <a name="ip-address-assignment-during-failover"></a>故障转移期间的 IP 地址分配

**源和目标子网** | **详细信息**
--- | ---
地址空间相同 | 源 VM NIC 的 IP 地址设置为目标 VM NIC IP 地址。<br/><br/> 如果该地址不可用，则将下一个可用 IP 地址设置为目标。

地址空间不同<br/><br/> 目标子网中下一个可用的 IP 地址设置为目标 VM NIC 地址。



## <a name="ip-address-assignment-during-test-failover"></a>测试故障转移期间的 IP 地址分配

**目标网络** | **详细信息**
--- | ---
目标网络是故障转移 VNet | - 目标 IP 地址是静态的，但不是为故障转移保留的同一 IP 地址。<br/><br/>  - 分配的地址是子网范围末段的下一个可用地址。<br/><br/> 例如：如果源 IP 地址是 10.0.0.19，故障转移网络使用范围 10.0.0.0/24，则分配给目标 VM 的下一个 IP 地址是 10.0.0.254。
目标网络不是故障转移 VNet | - 目标 IP 地址是静态的，与为故障转移保留的 IP 地址相同。<br/><br/>  - 如果已分配同一个 IP 地址，则 IP 地址是每个子网范围中的下一个可用地址。<br/><br/> 例如：如果源静态 IP 地址是 10.0.0.19，故障转移在范围为 10.0.0.0/24 的非故障转移网络中发生，则目标静态 IP 地址将是 10.0.0.0.19（如果该地址可用，否则是 10.0.0.254）。

- 故障转移 VNet 是设置灾难恢复时选择的目标网络。
- 我们建议始终使用非生产网络进行测试故障转移。
- 可以在 VM 的“计算和网络”设置中修改目标 IP 地址。


## <a name="next-steps"></a>后续步骤

- 查看 Azure VM 灾难恢复的[网络指导](site-recovery-azure-to-azure-networking-guidance.md)。
- [详细了解](site-recovery-retain-ip-azure-vm-failover.md)如何在故障转移后保留 IP 地址。

选择的目标网络是故障转移 VNet，但第 2 点指出“如果所选目标网络不同于故障转移 VNet，但子网范围与故障转移 VNet 相同”