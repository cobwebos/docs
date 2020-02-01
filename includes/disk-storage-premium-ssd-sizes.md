---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b2d4777772d842898cfcdd04f1c6d926cdbf0ad
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837555"
---
| 高级 SSD 大小 | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| 磁盘大小 (GiB) | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,096 | 8,192 | 16,384 | 32,767 |
| 每个磁盘的 IOPS | 120 | 120 | 120 | 120 | 240 | 500 | 1,100 | 2,300 | 5,000 | 7,500 | 7,500 | 16,000 | 18,000 | 20,000 |
| 每个磁盘的吞吐量 | 25 MiB/秒 | 25 MiB/秒 | 25 MiB/秒 | 25 MiB/秒 | 50 MiB/秒 | 100 MiB/秒 | 125 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒| 500 MiB/秒 | 750 MiB/秒 | 900 MiB/秒 |
| 每个磁盘的最大突发 IOPS** | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 |
| 每个磁盘的最大突发吞吐量** | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 |
| 最大突发持续时间** | 30 分钟  | 30 分钟  | 30 分钟  | 30 分钟  | 30 分钟  | 30 分钟  | 30 分钟  | 30 分钟  |
| 符合预留条件 | 否  | 否  | 否  | 否  | 否  | 否  | 否  | 否  | 是，最多一年 | 是，最多一年 | 是，最多一年 | 是，最多一年 | 是，最多一年 | 是，最多一年 |

\*表示目前处于预览状态的磁盘大小，有关区域可用性信息，请参阅[新磁盘大小：托管和非托管](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged)。  
\*\*表示目前处于预览状态的功能，有关详细信息，请参阅[磁盘突发](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability)。
