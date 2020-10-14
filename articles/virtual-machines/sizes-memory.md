---
title: Azure VM 大小 - 内存 | Microsoft Docs
description: 列出了 Azure 中虚拟机可用的不同内存优化大小。 列出了有关此系列中各大小的 vCPU 数、数据磁盘数和 NIC 数以及存储吞吐量和网络带宽的信息。
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
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 1e27ee2c045404d13b10945ac65484281b0cd967
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019709"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>内存优化虚拟机大小

内存优化 VM 规格提供适用于关系数据库服务器、大中型缓存和内存中分析的高内存 CPU 比率。 本文介绍了此分组中各个大小的 vCPU 数、数据磁盘数、NIC 数、存储吞吐量及网络带宽的相关信息。

- [Dv2 和 DSv2 系列](dv2-dsv2-series-memory.md)是原 D 系列的后续产品，具有更强大的 CPU。 Dv2 系列比 D 系列快大约 35%。 它在英特尔&reg; Xeon&reg; 8171M 2.1 GHz (Skylake)、英特尔&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) 或英特尔&reg; Xeon&reg; E5-2673 v3 2.4 GHz (Haswell) 处理器上运行，使用英特尔睿频加速技术 2.0。 Dv2 系列的内存和磁盘配置与 D 系列相同。

    Dv2 和 DSv2 系列非常适合于需要更快 vCPU、更佳临时存储性能或更高内存的应用程序。 它们为许多企业级应用程序提供强大的组合。

- 在拥有最多 256 MB L3 缓存的多线程配置中，[Eav4 和 Easv4 系列](eav4-easv4-series.md)使用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 处理器，增加了用于运行最多内存优化工作负载的选项。 Eav4 系列和 Easv4 系列具有与 Ev3 和 Esv3 系列相同的内存和磁盘配置。

- [Ev3 和 Esv3 系列](ev3-esv3-series.md)在超线程配置中使用英特尔&reg; Xeon&reg; 8171M 2.1 GHz (Skylake) 或英特尔&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) 处理器，为最常规用途的工作负载提供了更好的价值主张，因此 Ev3 适用于大多数其他云的常规用途 VM。 内存已扩展（从 7 GiB/vCPU 扩展到 8 GiB/vCPU），而磁盘和网络限制已基于每个核心进行了调整，以适应向超线程的转变。 Ev3 是 D/Dv2 系列的高内存 VM 大小产品的后继产品。

- [Ev4 和 Esv4 系列](ev4-esv4-series.md)在超线程配置中第2代的 Intel &reg; 强 &reg; 白金白金 8272CL (级联 Lake) 处理器上运行，适用于各种内存密集型企业应用程序和功能，最高可达 504 GiB 的 RAM。 它采用 intel AVX-512) 的 [intel &reg; Turbo 提升技术 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、 [Intel &reg; Hyper-Threading 技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 和 [intel &reg; 高级矢量扩展 512 (](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 Ev4 和 Esv4 不包含本地临时磁盘。 有关详细信息，请参阅  [没有本地临时磁盘的 AZURE VM 大小](azure-vms-no-temp-disk.md)。

- [Edv4 和 Edsv4 系列](edv4-edsv4-series.md)在第2代 Intel &reg; 强 &reg; 白金白金 8272CL (级联 Lake) 处理器上运行，非常适合极大型数据库或其他应用程序，这些应用程序受益于高 vCPU 计数和大量内存。 此外，这些 VM 大小包括快速、更大的本地 SSD 存储，适用于应用程序，这些应用程序受益于低延迟、高速本地存储。 它具有 3.4 GHz、 [intel &reg; 迅迅技术 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、 [Intel &reg; Hyper-Threading 技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 和 [INTEL &reg; 高级矢量扩展 512 (Intel AVX-512) ](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)的所有核心 Turbo 时钟速度。

- [M 系列](m-series.md)提供高 vCPU 计数（最多 128 个vCPU）和大量内存（最高 3.8 TiB）。 它也非常适用于极大型数据库或受益于 vCPU 数量多和内存大的其他应用程序。

- [Mv2 系列](mv2-series.md)为云中的任何 VM 提供最多 vCPU（最多 416 个 vCPU）和最大内存（最大 11.4 TiB）。 它非常适用于极大型数据库或受益于有许多 vCPU 和大量内存的其他应用程序。

Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。 这些虚拟机大小非常适合于与其他客户的工作负载（涉及符合性和法规要求等元素）高度隔离的工作负载。 客户还可以选择利用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，对这些独立的虚拟机资源进一步细分。 请参阅下面的虚拟机系列页面，了解独立 VM 选项。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。

有关 Azure 如何命名其 VM 的详细信息，请参阅 [Azure 虚拟机大小命名约定](./vm-naming-conventions.md)。
