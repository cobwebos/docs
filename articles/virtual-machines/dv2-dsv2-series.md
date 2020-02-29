---
title: Dv2 和 Dsv2 系列-Azure 虚拟机
description: Dv2 和 Dsv2 系列 Vm 的规格。
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164417"
---
# <a name="dv2-and-dsv2-series"></a>Dv2 和 DSv2 系列

Dv2 和 Dsv2 系列是原始 D 系列的后续功能，它具有更强大的 CPU 和最佳 CPU 到内存配置，使其适用于大多数生产工作负荷。 Dv2 系列比 D 系列快35%。 Dv2 系列在 Intel®强® 8171M 2.1 GHz （Skylake）、Intel®® E5-2673 v4 2.3 GHz （Broadwell）或 intel®至强® E5-2673 v3 2.4 GHz （Haswell）处理器上运行，并提供 Intel Turbo 提升技术2.0。 Dv2 系列的内存和磁盘配置与 D 系列相同。

## <a name="dv2-series"></a>Dv2 系列

Dv2 系列大小在 Intel®强® 8171M 2.1 GHz （Skylake）上运行，或者在 intel®强® E5-2673 v4 2.3 GHz （Broadwell）或 intel®至强® E5-2673 v3 2.0 2。4

ACU：210-250

高级存储：不支持

高级存储缓存：不支持

实时迁移：支持

内存保留更新：受支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量： IOPS/读取 MBps/写入 MBps | 最大数据磁盘数 | 吞吐量：IOPS | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3000/46/23    | 4  | 4x500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>DSv2-series

DSv2 系列大小运行在 Intel®强® 8171M 2.1 GHz （Skylake）或 Intel®强® E5-2673 v4 2.3 GHz （Broadwell）上，或 intel®至强® E5-2673 v3 2.4 GHz （Haswell）处理器，其中包含 Intel Turbo 提升技术2.0 并使用高级存储。

ACU：210-250

高级存储：支持

高级存储缓存：支持

实时迁移：支持

内存保留更新：受支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量： IOPS/MBps （GiB 中的缓存大小） | 最大非缓存磁盘吞吐量： IOPS/MBps | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 （43）    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 （86）    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 （172） | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 （344） | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 （688） | 51200/768 | 8/12000 |

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
