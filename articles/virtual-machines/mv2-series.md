---
title: Mv2 系列-Azure 虚拟机
description: Mv2 系列 Vm 的规格。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 21c28f3b6b498b162cbff276755b098c5ebba7c1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010472"
---
# <a name="mv2-series"></a>Mv2 系列

Mv2 系列提供高吞吐量、低延迟的平台，该平台在超线程 Intel®强®白金 GHz （Skylake）处理器上运行，所有核心基本频率为 2.5 GHz，最大 turbo 频率为 3.8 GHz。 所有 Mv2 系列虚拟机大小均可使用标准和高级永久性磁盘。 Mv2 系列实例是内存优化的 VM 大小，提供无与伦比的计算性能，可支持大型内存中数据库和工作负荷，并且非常适合用于关系数据库服务器、大型缓存和内存中分析。

Mv2 系列 VM 的 Intel®超线程技术功能

高级存储：支持

高级存储缓存：支持

实时迁移：不支持

内存保留更新：不支持

写入加速器：[支持](./windows/how-to-enable-write-accelerator.md)

|大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC 数/预期网络带宽 (MBps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 （7040） | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 （7040） | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 （14080） | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 （14080） | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Mv2 系列 vm 仅为第2代，并且支持第2代支持映像的子集。 请参阅下面的 Mv2 系列支持映像的完整列表。 如果你使用的是 Linux，请参阅对[Azure 上的第2代 vm 的支持](./linux/generation-2.md)，了解有关如何查找并选择映像的说明。 如果使用的是 Windows，请参阅对[Azure 上的第2代 vm 的支持](./windows/generation-2.md)，了解有关如何查找和选择映像的说明。 

- Windows Server 2019 或更高版本
- SUSE Linux Enterprise Server 12 SP4 及更高版本或 SUSE Linux Enterprise Server 15 SP1 及更高版本
- Red Hat Enterprise Linux 7.6、7.7、8.1 或更高版本 
- Oracle Enterprise Linux 7.7 或更高版本



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
