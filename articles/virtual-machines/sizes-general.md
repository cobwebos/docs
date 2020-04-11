---
title: Azure VM 大小 - 通用 |微软文档
description: 列出 Azure 中虚拟机可用的不同通用大小。 列出有关此系列中大小的数据 CPU、数据磁盘和 NIC 的数量以及存储吞吐量和网络带宽的信息。
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
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 22826c32dc2aee0e580ec0b2a05c7eb8f08b7570
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115311"
---
# <a name="general-purpose-virtual-machine-sizes"></a>常规用途虚拟机大小

常规用途 VM 大小提供均衡的 CPU 与内存之比。 适用于测试和开发、小到中型数据库和低到中等流量 Web 服务器。 本文提供有关通用计算产品的信息。

- [Av2 系列](av2-series.md)VM 可以部署在各种硬件类型和处理器上。 A 系列 VM 的 CPU 性能和内存配置非常适合部署和测试等入门级工作负荷。 根据硬件限制大小，为运行中的实例提供一致的处理器性能，不论硬件部署的位置。 若要判断此大小部署所在的物理硬件，请从虚拟机中查询虚拟硬件。 示例用例包括开发和测试服务器、低流量 Web 服务器、中小型数据库、概念证明和代码存储库。

  > [!NOTE]
  > A8 + A11 VM 计划于 2021 年 3 月 3 日停用。 有关详细信息，请参阅[HPC 迁移指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

- [B 系列可突发](sizes-b-series-burstable.md)VM 非常适合不需要 CPU 持续完全性能的工作负载，如 Web 服务器、小型数据库以及开发和测试环境。 这些工作负荷通常具有可突增的性能要求。 B 系列使这些客户能够购买具有高性价比基线性能的 VM 大小，允许 VM 实例在 VM 使用的性能小于其基线性能时积累积分。 如果 VM 已累积了积分，则 VM 可以在应用程序需要更高的 CPU 性能时突增到 VM 的基线之上，使用最多达到 100% 的 vCPU。

- [Dav4 和 Dasv4 系列](dav4-dasv4-series.md)采用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 处理器，采用多线程配置，将高达 256 MB L3 缓存的 8 MB 的 L3 缓存专用到每 8 个内核中，增加了客户运行通用工作负载的选项。 Dav4 系列和 Dasv4 系列具有与 D & Dsv3 系列相同的内存和磁盘配置。

- [DCv2 系列](dcv2-series.md)可帮助保护数据和代码在公共云中处理时的机密性和完整性。 这些机器由最新一代英特尔 XEON E-2288G 处理器支持，配备新GX技术。 借助英特尔涡轮提升技术，这些机器可高达 5.0GHz。 DCv2 系列实例使客户能够构建基于安全区的安全应用程序，以便在代码和数据使用时保护其代码和数据。

- [Dv2 和 Dsv2 系列](dv2-dsv2-series.md)VM 是原始 D 系列的追随者，具有更强大的 CPU 和最佳的 CPU 到内存配置，适合大多数生产工作负载。 Dv2 系列比 D 系列快大约 35%。 Dv2 系列在 Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 或 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 处理器上运行，并采用 Intel Turbo Boost Technology 2.0。 Dv2 系列的内存和磁盘配置与 D 系列相同。

- [Dv3 和 Dsv3 系列](dv3-dsv3-series.md)VM 在英特尔® Xeon ® 8171M 2.1GHz（Skylake）、英特尔® Xeon ® E5-2673 v4 2.3 GHz（Broadwell）或英特尔® Xeon ® E5-2673 v3 2.4 GHz （Haswell） 处理器上运行，为大多数通用工作负载提供更好的价值主张。 在磁盘和网络限制已基于核心进行了调整以适应超线程技术的同时，内存已扩展（从 ~3.5 GiB/vCPU 到 4 GiB/vCPU）。 Dv3 系列不再有 D/Dv2 系列的高内存 VM 大小，这些 VM 大小已移动到内存优化的[Ev3 和 Esv3 系列](ev3-esv3-series.md)。

示例 D 系列用例包括企业级应用程序、关系数据库、内存缓存和分析。

## <a name="other-sizes"></a>其他大小

- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
