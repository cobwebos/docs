---
title: Edv4 和 Edsv4 系列
description: 适用于 Ev4、Edv4、Esv4 和 Edsv4 系列 VM 的规格。
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 3456c37e066818f837bb0eb2f5aedb7c4db299d1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685198"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 和 Edsv4 系列

Edv4 和 Edsv4 系列在超线程配置的 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行，非常适合于各种占用大量内存的企业应用程序，配备高达 504 GiB 的 RAM、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)和 [Intel&reg; 高级矢量扩展 512 (intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 它们还支持[英特尔&reg; 深度学习加速技术](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)。 与 [Gen2 VM](./linux/generation-2.md) 的 [Ev3/Esv3](./ev3-esv3-series.md) 大小相比，这些新的 VM 大小有大 50% 的本地存储，同时有更好的读取与写入本地磁盘 IOPS

## <a name="edv4-series"></a>Edv4 系列

Edv4 系列尺寸在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行。 除了快速和大型本地 SSD 存储（最高为 2400 GiB）外，Edv4 虚拟机大小还配备高达 504 GiB 的 RAM。 这些虚拟机非常适合于内存密集型企业应用程序和受益于低延迟、高速本地存储的应用程序。 可以将标准 SSD 和标准 HDD 磁盘存储附加到 Edv4 VM。 

ACU：195 - 210

高级存储：不支持

高级存储缓存：不支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps | 最大 NIC 数|预期网络带宽 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


## <a name="edsv4-series"></a>Edsv4 系列

Edsv4 系列尺寸在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行。 除了快速和大型本地 SSD 存储（最高为 2400 GiB）外，Edsv4 虚拟机大小还配备高达 504 GiB 的 RAM。 这些虚拟机非常适合于内存密集型企业应用程序和受益于低延迟、高速本地存储的应用程序。

ACU：195-210

高级存储：支持

高级存储缓存：支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数|预期网络带宽 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211(500)  | 32000/480  | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936(800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [受约束的可用核心大小）](./constrained-vcpu.md)。


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器： [定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息： [磁盘类型](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。