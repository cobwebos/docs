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
ms.openlocfilehash: d2daafa6bf5f9a28ad2b61a97e7a8bd2246ae18d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65538404"
---
# <a name="what-disk-types-are-available-in-azure"></a>Azure 有哪些可用的磁盘类型？

Azure 托管磁盘目前提供四种磁盘类型，三种已正式发布 (GA)，一种为预览版。 这四种磁盘类型的每一种都有自己的相应目标客户方案。

## <a name="disk-comparison"></a>磁盘比较

下表对托管磁盘的超级固态驱动器（SSD，预览版）、高级 SSD、标准 SSD 和标准硬盘驱动器 (HDD) 进行了比较，方便你确定使用哪一种。

|   | 超级 SSD（预览版）   | 高级·SSD   | 标准 SSD   | 标准 HDD   |
|---------|---------|---------|---------|---------|
|磁盘类型   |SSD   |SSD   |SSD   |HDD   |
|场景   |SAP HANA、顶层数据库等 IO 密集型工作负荷（例如，SQL、Oracle），以及其他事务密集型工作负荷。   |生产和性能敏感型工作负荷   |Web 服务器、不常使用的企业应用程序和开发/测试   |备份、非关键、不常访问   |
|磁盘大小   |65,536 GB (GiB)（预览版）   |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大吞吐量   |2,000 MiB/秒（预览版）   |900 MiB/秒   |750 MiB/秒   |500 MiB/秒   |
|最大 IOPS   |160,000（预览版）   |20,000   |6,000   |2,000   |

## <a name="ultra-ssd-preview"></a>超级 SSD（预览版）

Azure 超级 SSD（预览版）为 Azure IaaS VM 提供高吞吐量、高 IOPS 和一贯低延迟的磁盘存储。 超级 SSD 的其他优势包括能够动态改变工作负荷以及磁盘的性能，而无需重启虚拟机。 超级 SSD 适用于 SAP HANA、顶层数据库等数据密集型工作负荷，以及事务密集型工作负荷。 超级 SSD 只可用作数据磁盘。 建议将高级 SSD 用作 OS 磁盘。

### <a name="performance"></a>性能

预配超级磁盘时，可以单独配置磁盘的容量和性能。 超级 SSD 提供从 4 GiB 到 64 TiB 这个范围的多种固定大小，并提供一个灵活的性能配置模型，用于单独配置 IOPS 和吞吐量。

超级 SSD 的一些重要功能包括：

- 磁盘容量：超级 SSD 的容量范围为 4 GiB 到 64 TiB。
- 磁盘 IOPS：超级 SSD 支持 300 IOPS/GiB 的 IOPS 限制，每个磁盘最高可达 160 K IOPS。 若要实现预配的 IOPS，请确保选择的磁盘 IOPS 小于 VM IOPS。 最小磁盘 IOPS 为 100 IOPS。
- 磁盘吞吐量：使用超级 SSD 时，单个磁盘对应于每个预配 IOPS 的吞吐量限制为 256 KiB/秒，每个磁盘的最大吞吐量为 2000 MBps（其中，MBps = 每秒 10^6 字节）。 最小磁盘吞吐量为 1 MiB。
- 超高的 Ssd 支持调整磁盘性能特性 （IOPS 和吞吐量） 在运行时无需分离的磁盘从虚拟机。 在对磁盘上发出磁盘性能调整大小操作后，更改实际上可能最多需要一个小时才能生效。

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

### <a name="transactions"></a>事务

对于超高 Ssd，每个 I/O 操作小于或等于为 256 KiB 吞吐量被视为单个 I/O 操作。 I/O 的吞吐量大于 256 KiB 操作被视为多个 I/o 大小为 256 KiB。

### <a name="preview-scope-and-limitations"></a>预览版的范围和限制

预览版超级 SSD 具有以下特点：

- 在美国东部 2 区的单个可用性区域中受支持  
- 只能在可用性区域中使用（区域外部的可用性集和单一 VM 部署无法附加超级磁盘）
- 仅在 ES/DS v3 VM 上受支持
- 只可用作数据磁盘，并且仅支持 4k 物理扇区大小  
- 只能以空磁盘的形式创建  
- 目前只能使用 Azure 资源管理器模板、CLI 和 Python SDK 进行部署。
- 尚不支持磁盘快照、VM 映像、可用性集、虚拟机规模集和 Azure 磁盘加密。
- 尚不支持与 Azure 备份或 Azure Site Recovery 集成。
- 与 [大多数预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)一样，在正式版 (GA) 发布之前，不应将此功能用于生产工作负荷。
