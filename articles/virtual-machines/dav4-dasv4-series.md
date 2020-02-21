---
title: Dav4 和 Dasv4 系列-Azure 虚拟机
description: Dav4 和 Dasv4 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f371281e4072902f9946eb9fd107b68a1e4bf4f6
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493873"
---
# <a name="dav4-and-dasv4-series"></a>Dav4 和 Dasv4 系列

Dav4 系列和 Dasv4 系列是在多线程配置中利用 AMD 的 2.35 Ghz EPYC<sup>TM</sup> 7452 处理器的新大小，具有高达 256 MB l3 缓存，专用于 8 GB 的 l3 缓存到每8个内核，从而提高了用于运行常规用途工作负载的客户选项。 Dav4 系列和 Dasv4 具有与 D & Dsv3 系列相同的内存和磁盘配置。

## <a name="dav4-series"></a>Dav4 系列

ACU：230-260

高级存储：不支持

高级存储缓存：不支持

Dav4 系列大小基于 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35 ghz 的提升最大频率。 Dav4 系列大小为大多数生产工作负荷提供 vCPU、内存和临时存储的组合。 数据磁盘存储与虚拟机分开计费。 若要使用高级 SSD，请使用 Dasv4 大小。 Dasv4 大小的定价和计费标准与 Dav4 系列相同。

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 临时存储的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大 Nic 数/预期网络带宽（MBps） |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000/46/23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000/93/46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000/187/93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000/375/187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000/750/375 |8 / 16000 |
| Standard_D48a_v4 <sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4 <sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup>这些大小处于预览阶段。  如果你有兴趣尝试这些更大的大小，请在[https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)上进行注册。

## <a name="dasv4-series"></a>Dasv4 系列

ACU：230-260

高级存储：支持

高级存储缓存：支持

Dasv4 系列大小基于 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35 ghz 的提升最大频率，并使用高级 SSD。 Dasv4 系列大小为大多数生产工作负荷提供 vCPU、内存和临时存储的组合。

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 Nic 数/预期网络带宽（MBps） |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 （50）|3200 / 48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000/64 （100）|6400/96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000/128 （200）|12800/192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 （400）|25600/384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 （800）|51200 / 768|8 / 16000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|96|384|768|32| | | 

<sup>**</sup>这些大小处于预览阶段。  如果你有兴趣尝试这些更大的大小，请在[https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)上进行注册。

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