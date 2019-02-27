---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443407"
---
**出站数据传输**：[出站数据传输](https://azure.microsoft.com/pricing/details/bandwidth/)（传出 Azure 数据中心的数据）会产生带宽使用费。

**事务**：会根据你对标准托管磁盘执行的事务数向你收费。 Azure 对标准 HDD 的每 100,000 个事务收费 0.0036 美元。 事务包括对存储区的读操作和写操作。

标准 SSD 使用大小为 256 KB 的 IO 单位。 如果要传输的数据小于 256 KB，该数据会被视为 1 个 I/O 单位。 更大的 I/O 大小被视为多个 256 KB 大小的 I/O。 例如，1,100 KB I/O 会被视为 5 个 I/O 单位。

高级托管磁盘没有事务费用。

有关托管磁盘的详细定价信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-ssd-vm-reservation-fee"></a>超级 SSD VM 预留费用

Azure VM 能够表明其是否兼容超级 SSD。 与超级磁盘兼容的 VM 在计算 VM 实例与块存储缩放单元之间分配专用的带宽容量，以优化性能并降低延迟。 在 VM 上添加此功能会导致预留费用，不过，这笔费用是仅当在 VM 上启用了超级磁盘功能但未将超级磁盘附加到 VM 时才产生的。 如果将超级磁盘附加到与超级磁盘兼容的 VM，则不会收取此费用。 此费用根据 VM 上预配的每个 vCPU 计收。

有关超级磁盘的定价细节，请参阅 [Azure 磁盘定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。