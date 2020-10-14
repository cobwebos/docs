---
title: Dv4 和 Dsv4 系列 - Azure 虚拟机
description: Dv4 和 Dsv4 系列 VM 的规范。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: ca01397959aa50c77218742320f8ced128876bad
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018672"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 和 Dsv4 系列

Dv4 和 Dsv4 系列以超线程配置在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行，为大多数常规用途工作负载带来价值提升。 它具有 3.4 GHz 的所有核心 Turbo 时钟速度。 

> [!NOTE]
> 有关常见问题解答，请参阅[无本地临时磁盘的 Azure VM 规格](azure-vms-no-temp-disk.md)。
## <a name="dv4-series"></a>Dv4 系列

Dv4 系列规格在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上运行。 Dv4 系列规格为大多数生产工作负载提供 vCPU、内存和远程存储选项组合。 Dv4 系列 VM 采用 [Intel&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。

远程数据磁盘存储与虚拟机分开计费。 若要使用高级存储磁盘，请使用 Dsv4 规格。 Dsv4 规格的定价和计费标准与 Dv4 系列的相同。

[ACU](acu.md)：195-210<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：受支持<br>
[VM 生成支持](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | 仅限远程存储 | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | 仅限远程存储 | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | 仅限远程存储 | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | 仅限远程存储 | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | 仅限远程存储 | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | 仅限远程存储 | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | 仅限远程存储 | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4 系列

Dsv4 系列规格在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上运行。 Dv4 系列规格为大多数生产工作负载提供 vCPU、内存和远程存储选项组合。 Dsv4 系列 VM 采用 [Intel&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。 远程数据磁盘存储与虚拟机分开计费。

[ACU](acu.md)：195-210<br>
[高级存储](premium-storage-performance.md)：受支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：受支持<br>
[VM 生成支持](generation-2.md)：第1代和第2代<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | 仅限远程存储 | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | 仅限远程存储 | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | 仅限远程存储 | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | 仅限远程存储 | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | 仅限远程存储 | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | 仅限远程存储 | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | 仅限远程存储 | 32 | 80000/1200 | 8|30000 |
