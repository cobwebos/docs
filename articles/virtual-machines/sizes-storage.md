---
title: Azure VM 大小-存储 |Microsoft Docs
description: 列出了可用于 Azure 中虚拟机的不同存储优化大小。 列出有关此系列中的大小的个 vcpu、数据磁盘和 Nic 的数量，以及存储吞吐量和网络带宽的信息。
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 17cceb04d3d6e559cd6f2b49674f6d408a0650a4
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492989"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>存储优化虚拟机大小

存储优化的 VM 大小提供高磁盘吞吐量和 IO，适用于大数据、SQL、NoSQL 数据库、数据仓库和大型事务数据库。  示例包括 Cassandra、MongoDB、Cloudera 和 Redis。 本文提供了有关每个优化大小的个 vcpu、数据磁盘和 Nic 的数量，以及本地存储吞吐量和网络带宽的信息。

[Lsv2 系列](lsv2-series.md)提供高吞吐量、低延迟、直接映射在[AMD EPYC<sup>TM</sup> 7551 处理器](https://www.amd.com/en/products/epyc-7000-series)上运行的本地 NVMe 存储，并具有 2.55 ghz 的所有核心提升和 3.0 ghz 的最大提升。 Lsv2 系列 VM 提供同时进行的多线程配置中的 8 到 80 个 vCPU。  每个 vCPU 有 8 GiB 内存，每 8 个 vCPU 有一个 1.92TB 的 NVMe SSD M.2 设备，并且在 L80s v2 上可以使用最多 19.2TB (10x1.92TB)。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。

了解如何在适用于[Windows](windows/storage-performance.md)或[Linux](linux/storage-performance.md)的 Lsv2 系列虚拟机上优化性能。