---
title: Azure VM 大小 - 存储 |微软文档
description: 列出 Azure 中可用于虚拟机的不同存储优化大小。 列出有关此系列中大小的数据 CPU、数据磁盘和 NIC 的数量以及存储吞吐量和网络带宽的信息。
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
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
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913330"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>存储优化虚拟机大小

存储优化的 VM 大小提供高磁盘吞吐量和 IO，是大数据、SQL、NoSQL 数据库、数据仓库和大型事务数据库的理想选择。  示例包括卡桑德拉、蒙戈DB、云泰拉和雷里斯。 本文提供有关每个优化大小的 vCPU、数据磁盘和 NIC 的数量以及本地存储吞吐量和网络带宽的信息。

[Lsv2 系列](lsv2-series.md)具有高吞吐量、低延迟、直接映射的本地 NVMe 存储，在[AMD EPYC<sup>TM</sup> 7551 处理器](https://www.amd.com/en/products/epyc-7000-series)上运行，所有核心提升为 2.55GHz，最大提升为 3.0GHz。 Lsv2 系列 VM 提供同时进行的多线程配置中的 8 到 80 个 vCPU。  每个 vCPU 有 8 GiB 内存，每 8 个 vCPU 有一个 1.92TB 的 NVMe SSD M.2 设备，并且在 L80s v2 上可以使用最多 19.2TB (10x1.92TB)。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。

了解如何优化[适用于 Windows](windows/storage-performance.md)或[Linux](linux/storage-performance.md)的 Lsv2 系列虚拟机的性能。
