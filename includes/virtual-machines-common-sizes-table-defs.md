---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "74279166"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>大小表定义

- 存储容量的单位为 GiB 或 1024^3 字节。 将以 GB （1000 ^ 3 字节）度量的磁盘与在 GiB （1024 ^ 3）中度量的磁盘进行比较时，请记住在 GiB 中指定的容量数量可能会更小。 例如，1023 GiB = 1098.4 GB。
- 磁盘吞吐量的单位为每秒输入/输出操作数 (IOPS) 和 MBps，其中 MBps = 10^6 字节/秒。
- 数据磁盘可以在缓存或非缓存模式下运行。 对于缓存数据磁盘操作，主机缓存模式设置为 **ReadOnly** 或 **ReadWrite**。  对于非缓存数据磁盘操作，主机缓存模式设置为 **None**。
- 如果要获得 Vm 的最佳性能，应将数据磁盘的数量限制为每个 vCPU 的两个磁盘。
- **预期网络带宽**是所有目标的所有 nic 上为每个 VM 类型分配的最大聚合带宽。 有关详细信息，请参阅[虚拟机网络带宽](../articles/virtual-network/virtual-machine-network-throughput.md)。

  上限不能保证。 限制为目标应用程序选择适当的 VM 类型提供指导。 实际的网络性能将取决于多种因素，包括网络拥塞、应用程序负载和网络设置。 有关优化网络吞吐量的信息，请参阅为[Azure 虚拟机优化网络吞吐量](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要在 Linux 或 Windows 上实现预期的网络性能，可能需要选择特定版本或优化 VM。 有关详细信息，请参阅[带宽/吞吐量测试（NTTTCP）](../articles/virtual-network/virtual-network-bandwidth-testing.md)。



