---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262195"
---
Azure 托管磁盘当前提供四种磁盘类型，每种类型都针对特定的客户方案。

## <a name="disk-comparison"></a>磁盘比较

下表提供了超级磁盘、高级固态硬盘 （SSD）、标准 SSD 和托管磁盘的标准硬盘驱动器 （HDD） 的比较，以帮助您决定使用什么。

|   | 超级磁盘   | 高级 SSD   | 标准 SSD   | 标准 HDD   |
|---------|---------|---------|---------|---------|
|磁盘类型   |SSD   |SSD   |SSD   |HDD   |
|方案   |IO 密集型工作负载，如[SAP HANA、](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)顶级数据库（例如 SQL、Oracle）和其他事务密集型工作负载。   |生产和性能敏感型工作负荷   |Web 服务器、不常使用的企业应用程序和开发/测试   |备份、非关键、不常访问   |
|最大磁盘大小   |65,536 GB (GiB)    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大吞吐量   |2,000 MiB/秒    |900 MiB/秒   |750 MiB/秒   |500 MiB/秒   |
|最大 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>超级磁盘

Azure 超级磁盘为 Azure IaaS VM 提供高吞吐量、高 IOPS 和一贯低延迟的磁盘存储。 超磁盘的一些其他好处包括能够动态更改磁盘的性能以及工作负载，而无需重新启动虚拟机 （VM）。 超级磁盘适用于 SAP HANA、顶层数据库等数据密集型工作负荷，以及事务密集型工作负荷。 超级磁盘只可用作数据磁盘。 建议将高级 SSD 用作 OS 磁盘。

### <a name="performance"></a>性能

预配超级磁盘时，可以单独配置磁盘的容量和性能。 Ultra 磁盘有多种固定尺寸，从 4 GiB 到 64 TiB，具有灵活的性能配置模型，允许您独立配置 IOPS 和吞吐量。

超磁盘的一些关键功能包括：

- 磁盘容量：超磁盘容量范围从 4 GiB 到 64 TiB。
- 磁盘 IOPS：超级磁盘支持 IOPS 限制 300 IOPS/GiB，每个磁盘最多 160 K IOPS。 要实现预配的 IOPS，请确保所选磁盘 IOPS 小于 VM IOPS 限制。 每个磁盘的最小保证 IOPS 为 2 IOPS/GiB，总基线最小为 100 IOPS。 例如，如果您有一个 4 GiB 超磁盘，您将拥有至少 100 个 IOPS，而不是 8 个 IOPS。
- 磁盘吞吐量：对于超磁盘，每个预配 IOPS 单个磁盘的吞吐量限制为 256 KiB/s，每个磁盘最多为 2000 MBps（其中 MBps = 10⁄6 字节/秒）。 每个预配 IOPS 的每个磁盘的最小保证吞吐量为 4KiB/s，总体基线最小为 1 MBps。
- Ultra 磁盘支持在运行时调整磁盘性能属性（IOPS 和吞吐量），而无需从虚拟机分离磁盘。 在对磁盘上发出磁盘性能调整大小操作后，更改实际上可能最多需要一个小时才能生效。 在 24 小时窗口内，有四个性能调整操作的限制。 由于缺少性能带宽容量，性能调整操作可能会失败。

### <a name="disk-size"></a>磁盘大小

|磁盘大小 (GiB)  |IOPS 上限  |吞吐量上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024 - 65,536（此范围内的大小以 1 TiB 为增量递增）     |160,000         |2,000         |

### <a name="ga-scope-and-limitations"></a>GA 范围和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
