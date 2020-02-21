---
title: Azure VM 大小-内存 |Microsoft Docs
description: 列出 Azure 中的虚拟机可用的不同内存优化大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines
documentationcenter: ''
author: jonbeck7
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
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493522"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>内存优化虚拟机大小

内存优化 VM 大小提供适用于关系数据库服务器、中到大型规模的缓存和内存中分析的高内存 CPU 比率。 本文介绍了此分组中各个大小的 vCPU 数、数据磁盘数、NIC 数、存储吞吐量及网络带宽的相关信息。

- [Dv2 和 DSv2 系列](dv2-dsv2-series-memory.md)是原始 D 系列的后续功能，具有更强大的 CPU。 Dv2 系列比 D 系列快35%。 它在 Intel®强® 8171M 2.1 GHz （Skylake）或 Intel®2673® E5-v4 2.3 GHz （Broadwell）或 Intel®至强® E5-2673 v3 2.4 GHz （Haswell）处理器上运行，并提供 Intel Turbo 提升技术2.0。 Dv2 系列的内存和磁盘配置与 D 系列相同。

    Dv2 和 DSv2 系列适用于需要更快的个 vcpu、更好的临时存储性能或更高内存需求的应用程序。 它们为许多企业级应用程序提供强大的组合。

- [Eav4 和 Easv4 系列](eav4-easv4-series.md)在多线程配置中使用 AMD 的 2.35 Ghz EPYC<sup>TM</sup> 7452 处理器，最多可使用256mb 三级缓存，同时增加了运行大多数内存优化工作负荷的选项。 Eav4 系列和 Easv4 具有与 Ev3 & Esv3 系列相同的内存和磁盘配置。

- [Ev3 和 Esv3 系列](ev3-esv3-series.md)Intel®强® 8171M 2.1 GHz （Skylake）或 Intel®强® E5-2673 V4 2.3 GHz （Broadwell）处理器，为大多数常规用途工作负荷提供更好的价值主张，并使 Ev3 与大多数其他云的通用 vm 保持一致。 内存已扩展（从 7 GiB/vCPU 到 8 GiB/vCPU），而磁盘和网络限制已基于核心进行了调整，以适应移动到超线程。 Ev3 是 D/Dv2 系列的高内存 VM 大小产品的后继产品。

- [M 系列](m-series.md)提供高 vCPU 计数（最高128个 vcpu）和大量内存（最大为 3.8 TiB）。 它也适用于极大型数据库或其他应用程序，这些应用程序可以受益于高 vCPU 计数和大量内存。

- [Mv2 系列](mv2-series.md)提供云中任意 VM 的最高 vCPU 计数（最高为416个 vcpu）和最大内存（最大为 8.19 TiB）。 它非常适用于极大型数据库或受益于高 vCPU 计数和大量内存的其他应用程序。

Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。 这些虚拟机大小非常适合于与其他客户的工作负载（涉及符合性和法规要求等元素）高度隔离的工作负载。 客户还可以选择利用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，对这些独立的虚拟机资源进一步细分。 请参阅下面的虚拟机系列页面获取隔离的 VM 选项。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。