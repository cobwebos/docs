---
title: Mv2 系列 - Azure 虚拟机
description: Mv2 系列 VM 的规格。
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 764dc93608ae3b8882b7048a722c6d3415cbc644
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885780"
---
# <a name="mv2-series"></a>Mv2 系列

Mv2 系列具有高吞吐量、低延迟平台，可在超线程英特尔® Xeon ®白金 8180M 2.5GHz（Skylake） 处理器上运行，所有核心基频率为 2.5 GHz，最大涡轮频率为 3.8 GHz。 所有 Mv2 系列虚拟机大小都可以使用标准和高级持久磁盘。 Mv2 系列实例是内存优化的 VM 大小，提供无与伦比的计算性能，支持大型内存数据库和工作负载，具有高内存 CPU 比率，非常适合关系数据库服务器、大型缓存和内存分析。

Mv2 系列 VM 的功能英特尔®超线程技术

高级存储：支持

高级存储缓存：支持

实时迁移：不支持

内存保留更新：不支持

写入加速器：[支持](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC 数/预期网络带宽 (MBps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Mv2 系列 VM 仅是第 2 代。 如果使用 Linux，请参阅 Azure[上支持第 2 代 VM](./linux/generation-2.md)了解如何查找和选择映像的说明。 如果使用 Windows，请参阅 Azure[上支持第 2 代 VM](./windows/generation-2.md)了解如何查找和选择映像的说明。 所有四个不同的 Mv2 系列 VM 类型列表所需的最小操作系统版本，如下所示：

- Windows Server 2019 或更高版本
- SUSE Linux 企业服务器 12 SP4 及更高版本或 SUSE Linux 企业服务器 15 SP1 及更高版本
- 红帽企业 Linux 7.6、 7.7、 8.1 或更高版本 
- 甲骨文企业Linux 7.7或更高版本



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
