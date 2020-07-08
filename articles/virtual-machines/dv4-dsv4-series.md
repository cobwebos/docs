---
title: Dv4 和 Dsv4 系列-Azure 虚拟机
description: Dv4 和 Dsv4 系列 Vm 的规格。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 68cd6673283362380fc5a1f4b780f0a22aa53402
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783506"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 和 Dsv4 系列

Dv4 和 Dsv4 系列在 &reg; 超线程配置的 Intel 强 &reg; 白金8272CL （级联 Lake）处理器上运行，为大多数通用工作负荷提供更好的价值主张。 它的特点是，3.4 GHz 的核心 Turbo 时钟速度持久。 

> [!NOTE]
> 对于常见问题，请参阅[没有本地临时磁盘的 AZURE VM 大小](azure-vms-no-temp-disk.md)。
## <a name="dv4-series"></a>Dv4 系列

Dv4 系列大小在 Intel &reg; 至强 &reg; 白金8272CL （Cascade Lake）上运行。 Dv4 系列大小为大多数生产工作负荷提供 vCPU、内存和远程存储选项的组合。 Dv4 系列 Vm[支持 Intel &reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。

远程数据磁盘存储与虚拟机分开计费。 若要使用高级存储磁盘，请使用 Dsv4 大小。 Dsv4 大小的定价和计费标准与 Dv4 系列相同。


> [!IMPORTANT]
> 这些新大小目前仅限公共预览。 可在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)注册这些 Dv4 和 Dsv4 系列。 


ACU：195-210

高级存储：不支持

高级存储缓存：不支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | 仅远程存储 | 4 | 2/1000 |
| Standard_D4_v4 | 4 | 16  | 仅远程存储 | 8 | 2/2000 |
| Standard_D8_v4 | 8 | 32 | 仅远程存储 | 16 | 4/4000 |
| Standard_D16_v4 | 16 | 64 | 仅远程存储 | 32 | 8/8000 |
| Standard_D32_v4 | 32 | 128 | 仅远程存储 | 32 | 8/16000 |
| Standard_D48_v4 | 48 | 192 | 仅远程存储 | 32 | 8/24000 |
| Standard_D64_v4 | 64 | 256 | 仅远程存储 | 32 | 8/30000 |

## <a name="dsv4-series"></a>Dsv4 系列

Dsv4 系列大小在 Intel &reg; 至强 &reg; 白金8272CL （Cascade Lake）上运行。 Dv4 系列大小为大多数生产工作负荷提供 vCPU、内存和远程存储选项的组合。 Dsv4 系列 Vm[支持 Intel &reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。 远程数据磁盘存储与虚拟机分开计费。

> [!IMPORTANT]
> 这些新大小目前仅限公共预览。 可在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)注册这些 Dv4 和 Dsv4 系列。 

ACU：195-210

高级存储：支持

高级存储缓存：支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存的最大吞吐量： IOPS/MBps （GiB 中的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | 仅远程存储 | 4 | 19000/120 （50） | 3000/48 | 2/1000 |
| Standard_D4s_v4 | 4 | 16 | 仅远程存储 | 8 | 38500/242 （100） | 6400/96 | 2/2000 |
| Standard_D8s_v4 | 8 | 32 | 仅远程存储 | 16 | 77000/485 （200） | 12800/192 | 4/4000 |
| Standard_D16s_v4 | 16 | 64  | 仅远程存储 | 32 | 154000/968 （400） | 25600/384 | 8/8000 |
| Standard_D32s_v4 | 32 | 128 | 仅远程存储 | 32 | 308000/1936 （800） | 51200/768 | 8/16000 |
| Standard_D48s_v4 | 48 | 192 | 仅远程存储 | 32 | 462000/2904 （1200） | 76800/1152 | 8/24000 |
| Standard_D64s_v4 | 64 | 256 | 仅远程存储 | 32 | 615000/3872 （1600） | 80000/1200 | 8/30000 |
