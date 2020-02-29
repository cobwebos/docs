---
title: 内存优化 Dv2 和 DSv2 系列 Vm-Azure 虚拟机
description: Dv2 和 DSv2 系列 Vm 的规格。
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 66e415070e60676df5602078aff50c7b68920f1e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914035"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>内存优化 Dv2 和 Dsv2 系列

Dv2 和 Dsv2 系列是原始 D 系列的后续功能，具有更强大的 CPU。 DSv2 系列大小运行在 Intel®强® 8171M 2.1 GHz （Skylake）或 Intel®强® E5-2673 v4 2.3 GHz （Broadwell）或 Intel®至强® E5-2673 v3 2.4 GHz （Haswell）处理器上。 Dv2 系列的内存和磁盘配置与 D 系列相同。

## <a name="dv2-series-11-15"></a>Dv2 系列 11-15

Dv2 系列大小运行在 Intel®强® 8171M 2.1 GHz （Skylake）或 Intel®强® E5-2673 v4 2.3 GHz （Broadwell）或 Intel®至强® E5-2673 v3 2.4 GHz （Haswell）处理器上。

ACU：210 - 250

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量： IOPS/读取 MBps/写入 MBps | 最大数据磁盘/吞吐量： IOPS | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8/25000 <sup>2</sup> |

<sup>1</sup> 实例对于专用于单个客户的硬件独立。
<sup>2</sup> 25000 Mbps，具有加速网络。

## <a name="dsv2-series-11-15"></a>DSv2 系列 11-15

DSv2 系列大小运行在 Intel®强® 8171M 2.1 GHz （Skylake）或 Intel®强® E5-2673 v4 2.3 GHz （Broadwell）或 Intel®至强® E5-2673 v3 2.4 GHz （Haswell）处理器上。

ACU：210 - 250 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量： IOPS/MBps （GiB 中的缓存大小） | 最大非缓存磁盘吞吐量： IOPS/MBps | 最大 Nic 数/预期网络带宽（Mbps） |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 （72）    | 6400/96   | 2/1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 （144） | 12800/192 | 4/3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 （288） | 25600/384 | 8/6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 （576） | 51200/768 | 8/12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 （720） | 64000/960 | 8/25000 <sup>4</sup> |

<sup>1</sup> DSv2 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅[为实现高性能而设计](./windows/premium-storage-performance.md)。
<sup>2</sup>实例隔离于基于 Intel Haswell 的硬件并专用于单个客户。  
<sup>3</sup> 受约束的可用核心大小。  
<sup>4</sup> 25000 Mbps，具有加速网络。

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
