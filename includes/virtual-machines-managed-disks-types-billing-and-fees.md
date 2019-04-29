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
ms.openlocfilehash: 3e303ce2b6f28500406bacf5b66d26f9c78ba46d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618011"
---
**出站数据传输**：[出站数据传输](https://azure.microsoft.com/pricing/details/bandwidth/)（传出 Azure 数据中心的数据）会产生带宽使用费。

**事务**：会根据你对标准托管磁盘执行的事务数向你收费。

有关托管磁盘定价的详细信息（包括事务成本），请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-ssd-vm-reservation-fee"></a>超级 SSD VM 预留费用

Azure VM 能够表明其是否兼容超级 SSD。 与超级磁盘兼容的 VM 在计算 VM 实例与块存储缩放单元之间分配专用的带宽容量，以优化性能并降低延迟。 在 VM 上添加此功能会导致预留费用，不过，这笔费用是仅当在 VM 上启用了超级磁盘功能但未将超级磁盘附加到 VM 时才产生的。 如果将超级磁盘附加到与超级磁盘兼容的 VM，则不会收取此费用。 此费用根据 VM 上预配的每个 vCPU 计收。

有关超级磁盘的定价细节，请参阅 [Azure 磁盘定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。