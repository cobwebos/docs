---
title: Azure VM 大小 - 内存 |微软文档
description: 列出 Azure 中虚拟机可用的不同内存优化大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM 隔离,隔离的 VM,隔离,隔离的
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 5a20e9c64b6ef948167333b54b16b34e84dc0e32
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273573"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>内存优化虚拟机大小

内存优化 VM 大小提供适用于关系数据库服务器、中到大型规模的缓存和内存中分析的高内存 CPU 比率。 本文介绍了此分组中各个大小的 vCPU 数、数据磁盘数、NIC 数、存储吞吐量及网络带宽的相关信息。

- [Dv2和DSv2系列](dv2-dsv2-series-memory.md)是原始D系列的后续，具有更强大的CPU功能。 Dv2 系列比 D 系列快大约 35%。 它运行&reg;在英特尔至强&reg;8171M 2.1 GHz （天空湖）&reg;或英特尔至&reg;强 E5-2673 v4 2.3 GHz （Broadwell） 或英特尔&reg;至强&reg;E5-2673 v3 2.4 GHz （哈斯韦尔） 处理器上，以及英特尔涡轮增压技术 2.0。 Dv2 系列的内存和磁盘配置与 D 系列相同。

    Dv2 和 DSv2 系列非常适合需要更快的 vCPU、更好的临时存储性能或具有更高内存需求的应用。 它们为许多企业级应用程序提供强大的组合。

- [Eav4 和 Easv4 系列](eav4-easv4-series.md)采用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 处理器，采用多线程配置，具有高达 256MB L3 缓存，增加了运行大多数内存优化工作负载的选项。 Eav4 系列和 Easv4 系列与 Ev3 & Esv3 系列具有相同的内存和磁盘配置。

- [Ev3 和 Esv3](ev3-esv3-series.md)系列&reg;英特尔 Xeon&reg; 8171M 2.1 GHz（天&reg;湖）或&reg;英特尔 Xeon E5-2673 v4 2.3 GHz （Broadwell） 处理器具有超线程配置，为大多数通用工作负载提供了更好的价值主张，使 Ev3 与大多数其他云的通用 VM 保持一致。 内存已扩展（从 7 GiB/vCPU 扩展为 8 GiB/vCPU），同时每个内核对磁盘和网络限制进行了调整，以便与移动到超线程一致。 Ev3 是 D/Dv2 系列的高内存 VM 大小产品的后继产品。

- [M 系列](m-series.md)提供高 vCPU 计数（最多 128 个 vCPU）和大量内存（高达 3.8 TiB）。 它也非常适用于极大型数据库或受益于高 vCPU 计数和大量内存的其他应用程序。

- [Mv2 系列](mv2-series.md)提供云中任何 VM 中最高的 vCPU 计数（最多 416 个 vCPU）和最大的内存（最多 11.4 TiB）。 它非常适用于极大型数据库或受益于高 vCPU 计数和大量内存的其他应用程序。

Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。 这些虚拟机大小非常适合于与其他客户的工作负载（涉及符合性和法规要求等元素）高度隔离的工作负载。 客户还可以选择利用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，对这些独立的虚拟机资源进一步细分。 有关隔离的 VM 选项，请参阅下面的虚拟机系列页面。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
