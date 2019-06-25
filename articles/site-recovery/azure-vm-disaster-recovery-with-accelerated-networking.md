---
title: 将加速网络与 Azure 虚拟机灾难恢复配合使用 | Microsoft Docs
description: 介绍了如何将加速网络与 Azure Site Recovery 配合使用来实现 Azure 虚拟机灾难恢复
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: c7edc7979636ced8697aa5ad724f9c6600d840bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772458"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>将加速网络与 Azure 虚拟机灾难恢复配合使用

使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。 下图显示了具有和没有加速网络的两个 VM 之间的通信：

![比较](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

对于故障转移到不同 Azure 区域的 Azure 虚拟机，Azure Site Recovery 允许你利用加速网络的优势。 本文介绍了如何为通过 Azure Site Recovery 复制的 Azure 虚拟机启用加速网络。

## <a name="prerequisites"></a>必备组件

在开始之前，请确保了解：
-   Azure 虚拟机[复制体系结构](azure-to-azure-architecture.md)
-   为 Azure 虚拟机[设置复制](azure-to-azure-tutorial-enable-replication.md)
-   [故障转移](azure-to-azure-tutorial-failover-failback.md) Azure 虚拟机

## <a name="accelerated-networking-with-windows-vms"></a>Windows VM 的加速网络

只有当源虚拟机已启用了加速网络时，Azure Site Recovery 才支持为复制的虚拟机启用加速网络。 如果源虚拟机未启用加速网络，可以[从此处](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)了解如何为 Windows 虚拟机启用加速网络。

### <a name="supported-operating-systems"></a>支持的操作系统
从 Azure 库即可支持以下分发：
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>支持的 VM 实例
大多数常规用途实例以及具有 2 个或更多 vCPU 的计算优化实例都支持加速网络。  这些受支持的系列包括：D/DSv2 和 F/Fs

在支持超线程的实例上，具有 4 个或更多 vCPU 的 VM 实例支持加速网络。 受支持的系列包括：D/DSv3、E/ESv3、Fsv2 和 Ms/Mms

有关 VM 实例的详细信息，请参阅[Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="accelerated-networking-with-linux-vms"></a>Linux VM 的加速网络

只有当源虚拟机已启用了加速网络时，Azure Site Recovery 才支持为复制的虚拟机启用加速网络。 如果源虚拟机未启用加速网络，可以[从此处](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)了解如何为 Linux 虚拟机启用加速网络。

### <a name="supported-operating-systems"></a>支持的操作系统
从 Azure 库即可支持以下分发：
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian“Stretch”（backport 内核）**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>支持的 VM 实例
大多数常规用途实例以及具有 2 个或更多 vCPU 的计算优化实例都支持加速网络。  这些受支持的系列包括：D/DSv2 和 F/Fs

在支持超线程的实例上，具有 4 个或更多 vCPU 的 VM 实例支持加速网络。 受支持的系列包括：D/DSv3、E/ESv3、Fsv2 和 Ms/Mms。

有关 VM 实例的详细信息，请参阅[Linux VM 大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>为复制的 VM 启用加速网络

为 Azure 虚拟机[启用复制](azure-to-azure-tutorial-enable-replication.md)时，Site Recovery 将自动检测虚拟机网络接口是否启用了加速网络。 如果已启用加速网络，则 Site Recovery 将自动在复制的虚拟机的网络接口上配置加速网络。

可以在复制的虚拟机的“计算和网络”设置的“网络接口”部分下验证加速网络的状态。  

![加速网络设置](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

如果在启用复制后在源虚拟机上启用了加速网络，则可以通过以下过程为复制的虚拟机的网络接口启用加速网络：
1. 打开复制的虚拟机的“计算和网络”  设置
2. 在“网络接口”部分下单击网络接口的名称 
3. 在“目标”列下，从“加速网络”的下拉列表中选择“启用”。  

![启用加速网络](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

对于 Site Recovery 以前未自动启用加速网络的现有复制虚拟机，也应当遵循上述过程。

## <a name="next-steps"></a>后续步骤
- 详细了解[加速网络的优势](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)。
- 详细了解针对 [Windows 虚拟机](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints)和 [Linux 虚拟机](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints)的加速网络限制和约束。
- 详细了解如何使用[恢复计划](site-recovery-create-recovery-plans.md)自动执行应用程序故障转移。
