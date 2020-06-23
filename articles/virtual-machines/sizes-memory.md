---
title: Azure VM 大小 - 内存 | Microsoft Docs
description: 列出 Azure 中适用于虚拟机的各种内存优化大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
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
ms.openlocfilehash: ab4c95e3525caa42b979b15fa8118e9dd21bb74a
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248288"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>内存优化虚拟机大小

内存优化 VM 大小提供适用于关系数据库服务器、中到大型规模的缓存和内存中分析的高内存 CPU 比率。 本文介绍了此分组中各个大小的 vCPU 数、数据磁盘数、NIC 数、存储吞吐量及网络带宽的相关信息。

- [Dv2 和 DSv2 系列](dv2-dsv2-series-memory.md)是原 D 系列的后续系列，其特点是 CPU 功能更强大。 Dv2 系列比 D 系列快大约 35%。 它在英特尔&reg; Xeon&reg; 8171M 2.1 GHz (Skylake)、英特尔&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) 或英特尔&reg; Xeon&reg; E5-2673 v3 2.4 GHz (Haswell) 处理器上运行，使用英特尔睿频加速技术 2.0。 Dv2 系列的内存和磁盘配置与 D 系列相同。

    Dv2 和 DSv2 系列非常适合要求具有更快的 vCPU、更好的临时存储性能或更高内存的应用程序。 它们为许多企业级应用程序提供强大的组合。

- 在拥有最多 256 MB L3 缓存的多线程配置中，[Eav4 和 Easv4 系列](eav4-easv4-series.md)使用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 处理器，增加了用于运行最多内存优化工作负载的选项。 Eav4 系列和 Easv4 系列具有与 Ev3 和 Esv3 系列相同的内存和磁盘配置。

- [Ev3 和 Esv3 系列](ev3-esv3-series.md)在超线程配置中使用英特尔&reg; Xeon&reg; 8171M 2.1 GHz (Skylake) 或英特尔&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) 处理器，为最常规用途的工作负载提供了更好的价值主张，因此 Ev3 适用于大多数其他云的常规用途 VM。 在磁盘和网络限制已基于核心进行了调整以适应超线程技术的同时，内存也得到了扩展（从 7 GiB/vCPU 到 8 GiB/vCPU）。 Ev3 是 D/Dv2 系列的高内存 VM 大小产品的后继产品。

- [Edv4 和 Edsv4 系列](edv4-edsv4-series.md)在第 2 代英特尔&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行，非常适合特别大的数据库或者其他受益于 vCPU 数多和内存丰富的应用程序。 它的持续全核睿频时钟速度为 3.4 GHz，采用[英特尔&reg; 睿频加速技术 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[英特尔&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)和[英特尔&reg; 高级矢量扩展 512（英特尔 AVX-512）](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 

- [M 系列](m-series.md)提供许多 vCPU（最多 128 个 vCPU）和大量内存（最大 3.8 TiB）。 它也非常适用于极大型数据库或受益于 vCPU 数量多和内存大的其他应用程序。

- [Mv2 系列](mv2-series.md)为云中的任何 VM 提供最多 vCPU（最多 416 个 vCPU）和最大内存（最大 11.4 TiB）。 它非常适用于极大型数据库或受益于有许多 vCPU 和大量内存的其他应用程序。

Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。 这些虚拟机大小非常适合于与其他客户的工作负载（涉及符合性和法规要求等元素）高度隔离的工作负载。 客户还可以选择利用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，对这些独立的虚拟机资源进一步细分。 请参阅下面关于虚拟机系列的页面，了解独立 VM 选项。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
