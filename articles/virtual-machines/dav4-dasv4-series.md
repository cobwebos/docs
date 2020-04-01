---
title: 达夫4和Dasv4系列 - Azure虚拟机
description: Dav4 和 Dasv4 系列 VM 的规格。
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 56e86aa75b153b5cb005c96fca45373d30ffa8b4
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437093"
---
# <a name="dav4-and-dasv4-series"></a>Dav4 和 Dasv4 系列

Dav4 系列和 Dasv4 系列采用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 处理器，采用多线程配置，将高达 256 MB L3 缓存的 8 GB L3 缓存专用于每 8 个内核中 8 GB，增加了客户运行通用工作负载的选项。 Dav4 系列和 Dasv4 系列具有与 D & Dsv3 系列相同的内存和磁盘配置。

## <a name="dav4-series"></a>达夫4系列

ACU： 230-260

高级存储：不支持

高级存储缓存：不支持

实时迁移：支持

内存保留更新：受支持

Dav4 系列尺寸基于 2.35Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35GHz 的提升最大频率。 Dav4 系列尺寸为大多数生产工作负载提供 vCPU、内存和临时存储的组合。 数据磁盘存储与虚拟机分开计费。 要使用高级 SSD，请使用 Dasv4 大小。 Dasv4 尺寸的定价和计费表与 Dav4 系列相同。

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 临时存储的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大 NIC / 预期网络带宽 （MBps） |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000/46/23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000/93/46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000/187/93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000/375/187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000/750/375 |8 / 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000 / 1000 / 500 | 8/24000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000 / 1000 / 500 | 8 / 30000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000 / 1000 / 500 | 8 / 30000 |

## <a name="dasv4-series"></a>达斯夫4系列

ACU： 230-260

高级存储：支持

高级存储缓存：支持

实时迁移：支持

内存保留更新：受支持

Dasv4 系列尺寸基于 2.35Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35GHz 的提升最大频率并使用高级 SSD。 Dasv4 系列尺寸为大多数生产工作负载提供 vCPU、内存和临时存储的组合。

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC / 预期网络带宽 （MBps） |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_D48as_v4|48|192|384|32|96000 / 1020 (1200)|76800 / 1148|8/24000 |
| Standard_D64as_v4|64|256|512|32|128000 / 1020 (1600)|80000 / 1200|8 / 30000 | 
| Standard_D96as_v4|96|384|768|32|192000 / 1020 (2400)|80000 / 1200|8 / 30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
