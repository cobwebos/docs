---
title: Azure VM 大小-常规用途 |Microsoft Docs
description: 列出 Azure 中的虚拟机可用的不同常规用途大小。 列出有关此系列中的大小的个 vcpu、数据磁盘和 Nic 的数量，以及存储吞吐量和网络带宽的信息。
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
ms.openlocfilehash: d8a13d9013b2ad526c8e332cf3ea74e04f253d98
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597705"
---
# <a name="general-purpose-virtual-machine-sizes"></a>常规用途虚拟机大小

常规用途 VM 大小提供均衡的 CPU 与内存之比。 适用于测试和开发、小到中型数据库和低到中等流量 Web 服务器。 本文提供了有关常规用途计算的信息。

- [Av2 系列](av2-series.md)vm 可以部署在各种不同的硬件类型和处理器上。 A 系列 VM 的 CPU 性能和内存配置非常适合部署和测试等入门级工作负荷。 根据硬件限制大小，为运行中的实例提供一致的处理器性能，不论硬件部署的位置。 若要判断此大小部署所在的物理硬件，请从虚拟机中查询虚拟硬件。 示例用例包括开发和测试服务器、低流量 Web 服务器、中小型数据库、概念证明和代码存储库。

- [B 系列可突增](sizes-b-series-burstable.md)Vm 非常适合于不需要持续占用 CPU 完全性能的工作负荷，如 web 服务器、小型数据库以及开发和测试环境。 这些工作负荷通常具有可突增的性能要求。 B 系列使这些客户能够购买具有高性价比基线性能的 VM 大小，允许 VM 实例在 VM 使用的性能小于其基线性能时积累积分。 如果 VM 已累积了积分，则 VM 可以在应用程序需要更高的 CPU 性能时突增到 VM 的基线之上，使用最多达到 100% 的 vCPU。

- [Dav4 和 Dasv4 系列](dav4-dasv4-series.md)是在多线程配置中利用 AMD 的 2.35 Ghz EPYC<sup>TM</sup> 7452 处理器的新大小，具有高达 256 MB 三级缓存，专用于 8 GB 的 l3 缓存到每8个内核，增加了用于运行常规用途工作负荷的客户选项。 Dav4 系列和 Dasv4 具有与 D & Dsv3 系列相同的内存和磁盘配置。

- 在公有云中处理数据和代码时， [DCv2 系列](dcv2-series.md)可帮助保护数据和代码的机密性和完整性。 这些计算机由最新的具有 SGX 技术的最新的 Intel 2288G 处理器生成提供支持。 利用 Intel Turbo 提升技术，这些计算机可以高达 5.0 GHz。 DCv2 系列实例使客户能够构建安全的基于 enclave 的应用程序，以在使用时保护其代码和数据。

- [Dv2 和 Dsv2 系列](dv2-dsv2-series.md)Vm 是原始 D 系列的跟进，它具有更强大的 CPU 和最佳 CPU 到内存配置，使其适用于大多数生产工作负荷。 Dv2 系列比 D 系列快35%。 Dv2 系列在 Intel®强® 8171M 2.1 GHz （Skylake）、Intel®® E5-2673 v4 2.3 GHz （Broadwell）或 intel®至强® E5-2673 v3 2.4 GHz （Haswell）处理器上运行，并提供 Intel Turbo 提升技术2.0。 Dv2 系列的内存和磁盘配置与 D 系列相同。

- [Dv3 和 Dsv3 系列](dv3-dsv3-series.md)Vm 在超线程配置中运行在 Intel®强® 8171M 2.1 GHz （Skylake）、Intel®2673® E5-v4 2.3 GHz （Broadwell）或 Intel®至强® E5-2673 v3 2.4 GHz （Haswell）处理器上，为大多数常规用途工作负荷提供更好的价值主张。 在磁盘和网络限制已基于核心进行了调整以适应超线程技术的同时，内存已扩展（从 ~3.5 GiB/vCPU 到 4 GiB/vCPU）。 Dv3 系列不再具有 D/Dv2 系列的高内存 VM 大小，它们已被移动到内存优化[Ev3 和 Esv3 系列](ev3-esv3-series.md)。

D 系列用例示例包括企业级应用程序、关系数据库、内存中缓存和分析。

## <a name="other-sizes"></a>其他大小

- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。