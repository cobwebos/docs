---
title: Azure VM 大小 - 计算优化 |微软文档
description: 列出 Azure 中虚拟机可用的不同计算优化大小。 列出有关此系列中大小的数据 CPU、数据磁盘和 NIC 的数量以及存储吞吐量和网络带宽的信息。
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e08d593f641c42f9ad605fda013206e70a34e52f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269629"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>计算优化虚拟机大小

计算优化的 VM 大小具有较高的 CPU 内存比。 这些大小适用于中流量 Web 服务器、网络设备、批处理和应用程序服务器。 本文提供有关 vCPU、数据磁盘和 NIC 的数量的信息。 它还包括有关此分组中每个大小的存储吞吐量和网络带宽的信息。

[Fsv2 系列](fsv2-series.md)基于英特尔® Xeon ®白金 8168 处理器。 它具有持续全芯涡轮时钟速度为 3.4 GHz 和最大单核涡轮频率为 3.7 GHz。 英特尔® AVX-512 指令是英特尔可扩展处理器上的新指令。 这些指令在单精度和双精度浮点操作上为矢量处理工作负载提供高达 2 倍的性能提升。 换句话说，对于任何计算工作负载来说，它们都非常快。

凭借较低的每小时定价，Fsv2 系列在基于每个 vCPU 的 Azure 计算单位 (ACU) 的 Azure 产品组合中具有最高性价比。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
