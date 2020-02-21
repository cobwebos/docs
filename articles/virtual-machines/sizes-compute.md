---
title: Azure VM 大小-计算优化 |Microsoft Docs
description: 列出了可用于 Azure 中虚拟机的各种计算优化大小。 列出有关此系列中的大小的个 vcpu、数据磁盘和 Nic 的数量，以及存储吞吐量和网络带宽的信息。
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
ms.openlocfilehash: d709d621341ef14ec158ed5af1c2df4297d66b8b
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493626"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>计算优化虚拟机大小

计算优化 VM 大小的 CPU 与内存的比率较高。 这些大小适用于中等流量的 web 服务器、网络设备、批处理和应用程序服务器。 本文提供有关个 vcpu、数据磁盘和 Nic 的数量的信息。 它还包括有关此分组中每个大小的存储吞吐量和网络带宽的信息。

[Fsv2 系列](fsv2-series.md)基于 Intel®强®白金8168处理器。 它的核心 Turbo 时钟速度始终为 3.4 GHz，最大单核 turbo 主频为 3.7 GHz。 Intel® AVX-512 说明是 Intel 可伸缩处理器上的新说明。 这些说明为单精度和双精度浮点运算上的矢量处理工作负荷提供了高达2倍的性能提升。 换句话说，对于任何计算工作负荷，它们都非常快。

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