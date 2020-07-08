---
title: Azure VM 大小 - 常规用途 | Microsoft Docs
description: 列出 Azure 中虚拟机进行常规用途所适用的各种大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: 1e9cce7adc6dbc9062dd133d690fbce92c3190fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783310"
---
# <a name="general-purpose-virtual-machine-sizes"></a>常规用途虚拟机大小

常规用途 VM 大小提供均衡的 CPU 与内存之比。 适用于测试和开发、小到中型数据库和低到中等流量 Web 服务器。 本文提供常规用途计算产品/服务的相关信息。

- [Av2 系列](av2-series.md) VM 可以部署在各种不同的硬件类型和处理器上。 A 系列 VM 的 CPU 性能和内存配置非常适合部署和测试等入门级工作负荷。 根据硬件限制大小，为运行中的实例提供一致的处理器性能，不论硬件部署的位置。 若要判断此大小部署所在的物理硬件，请从虚拟机中查询虚拟硬件。 示例用例包括开发和测试服务器、低流量 Web 服务器、中小型数据库、概念证明和代码存储库。

  > [!NOTE]
  > A8 – A11 VM 计划于 2021 年 3 月停用。 有关详细信息，请参阅 [HPC 迁移指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

- [B 系列可激增 VM](sizes-b-series-burstable.md) 非常适合不持续需要最佳 CPU 性能的工作负荷，例如 Web 服务器、小型数据库及开发和测试环境。 这些工作负荷通常具有可突增的性能要求。 B 系列使这些客户能够购买具有高性价比基线性能的 VM 大小，允许 VM 实例在 VM 使用的性能小于其基线性能时积累积分。 如果 VM 已累积了积分，则 VM 可以在应用程序需要更高的 CPU 性能时突增到 VM 的基线之上，使用最多达到 100% 的 vCPU。

- [Dav4 和 Dasv4 系列](dav4-dasv4-series.md)采用新尺寸，使用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 处理器，采用多线程配置，提供高达 256 MB 的 L3 缓存，每 8 个内核分配 8 MB 的专用 L3 缓存，从而为客户提供了更多运行常规用途工作负载的选项。 Dav4 系列和 Dasv4 系列具有与 D 和 Dsv3 系列相同的内存和磁盘配置。

- [DCsv2 系列](dcv2-series.md)可以在公有云中处理数据和代码时帮助保护数据和代码的机密性和完整性。 这些虚拟机由最新一代采用 SGX 技术的 Intel XEON E-2288G 处理器提供支持。 借助 Intel Turbo Boost Technology，这些虚拟机最高可以达到 5.0GHz。 客户使用 DCv2 系列实例可以构建安全的基于 enclave 的应用程序，以在使用该系列时保护其代码和数据。

- [Dv2 和 Dsv2 系列](dv2-dsv2-series.md) VM（原始 D 系列的后续产品）提供更强大的 CPU 和最优 CPU 到内存的配置，使其可适合大多数生产工作负荷。 Dv2 系列比 D 系列快大约 35%。 Dv2 系列于第2代 Intel®强®白金8272CL （级联 Lake）、Intel®强® 8171M 2.1 GHz （Skylake）、Intel®至强® E5-2673 v4 2.3 GHz （Broadwell）或 Intel®至强® E5-2673 v3 2.0 2。4 Dv2 系列的内存和磁盘配置与 D 系列相同。

- [Dv3 和 Dsv3 系列](dv3-dsv3-series.md)在第二代上，Vm 运行的是 Intel®强®白金8272CL （级联 Lake）、Intel® Skylake® 8171M 2.1 GHz （）、Intel®至强® E5-2673 v4 2.3 GHz （Broadwell）或 Intel®至强® E5-2673 v3 2。4 在磁盘和网络限制已基于核心进行了调整以适应超线程技术的同时，内存已扩展（从 ~3.5 GiB/vCPU 到 4 GiB/vCPU）。 Dv3 系列不再具备 D/Dv2 系列的高端存储器 VM 大小，后者已转至经过内存优化的 [Ev3 和 Esv3 系列](ev3-esv3-series.md)。

- [Dv4 和 Dsv4 系列](dv4-dsv4-series.md)Vm 在第2代 Intel &reg; 强 &reg; 白金8272CL （级联 Lake）处理器上运行，为大多数通用工作负荷提供更好的价值主张。 Dv4 和 Dsv4 Vm 系列是一种新类别的虚拟机，它依赖于远程磁盘，不提供临时本地存储。 它的持续全核睿频时钟速度为 3.4 GHz，采用[英特尔&reg; 睿频加速技术 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[英特尔&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)和[英特尔&reg; 高级矢量扩展 512（英特尔 AVX-512）](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 

- [Ddv4 和 Ddsv4 系列](ddv4-ddsv4-series.md)Vm 在第2代 Intel &reg; 强 &reg; 白金8272CL （级联 Lake）处理器上运行，为大多数通用工作负荷提供更好的价值主张。 Ddv4 和 Ddsv4 Vm 系列是包含本地数据临时磁盘的一种新的虚拟机类别。 它具有 3.4GHz 的持续全核 Turbo 时钟速度，并采用 [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)和 [Intel&reg; 高级矢量扩展 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 

## <a name="other-sizes"></a>其他大小

- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
