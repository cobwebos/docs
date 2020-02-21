---
title: Eav4 系列和 Easv4 系列-Azure 虚拟机
description: Eav4 和 Easv4 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 96cd7b4f4627e7c0335df9c745d8193a3dac7044
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493769"
---
# <a name="eav4-and-easv4-series"></a>Eav4 和 Easv4 系列

Eav4 系列和 Easv4 系列在多线程配置中利用 AMD 的 2.35 Ghz EPYC<sup>TM</sup> 7452 处理器，最多可使用256Mb 三级缓存，同时增加了运行大多数内存优化工作负荷的选项。 Eav4 系列和 Easv4 具有与 Ev3 & Esv3 系列相同的内存和磁盘配置。

## <a name="eav4-series"></a>Eav4 系列

ACU： 230-260

高级存储：不支持

高级存储缓存：不支持

Eav4 系列大小基于 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35 ghz 的提升最大频率，并使用高级 SSD。 Eav4 系列大小适用于内存密集型企业应用程序。 数据磁盘存储与虚拟机分开计费。 若要使用高级 SSD，请使用 Easv4 系列大小。 Easv4 大小的定价和计费标准与 Eav3 系列相同。

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 临时存储的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大 Nic 数/预期网络带宽（MBps） |
| -----|-----|-----|-----|-----|-----|-----|
| 标准\_E2a\_v4|2|16|50|4|3000/46/23|2 / 1000 |
| 标准\_E4a\_v4|4|32|100|8|6000/93/46|2 / 2000 |
| 标准\_E8a\_v4|8|64|200|16|12000/187/93|4 / 4000 |
| 标准\_E16a\_v4|16|128|400|32|24000/375/187|8 / 8000 |
| 标准\_E20a\_v4|20|160|500|32|30000/468/234|8 / 10000 |
| 标准\_E32a\_v4|32|256|800|32|48000/750/375|8 / 16000 |
| 标准\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| 标准\_E64a\_v4 <sup>**</sup> |64|512|1600|32| | |
| 标准\_E96a\_v4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup> 这些大小处于预览阶段。 如果你有兴趣尝试这些更大的大小，请在[https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)上进行注册。

## <a name="easv4-series"></a>Easv4 系列

ACU： 230-260

高级存储：支持

高级存储缓存：支持

Easv4 系列大小基于 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35 ghz 的提升最大频率，并使用高级 SSD。 Easv4 系列大小适用于内存密集型企业应用程序。

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 Nic 数/预期网络带宽（MBps） |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 （50）|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 （100）|6400/96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 （200）|12800/192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 （400）|25600/384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 （500）|32000/480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 （800）|51200 / 768|8 / 16000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup> 这些大小处于预览阶段。 如果你有兴趣尝试这些更大的大小，请在[https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)上进行注册。

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