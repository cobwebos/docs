---
title: 高级页 blob 存储帐户的可伸缩性目标
titleSuffix: Azure Storage
description: 高级性能页 blob 存储帐户已针对读取/写入操作进行了优化。 这种类型的存储帐户为 Azure 虚拟机支持非托管磁盘。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 1542204a2ac779a711407ab1a36c6f8ab9669510
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483526"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>高级页 blob 存储帐户的可伸缩性和性能目标

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>高级页 blob 帐户的缩放目标

高级性能页 blob 存储帐户已针对读取/写入操作进行了优化。 这种类型的存储帐户为 Azure 虚拟机支持非托管磁盘。

> [!NOTE]
> Microsoft 建议尽可能使用 Azure 虚拟机（Vm）的托管磁盘。 有关托管磁盘的详细信息，请参阅[Windows vm Azure 磁盘存储概述](../../virtual-machines/windows/managed-disks-overview.md)。

高级页 blob 存储帐户具有以下可伸缩性目标：

| 总帐户容量                            | 本地冗余存储帐户的总带宽                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| 磁盘容量：35 TB <br>快照容量：10 TB | 为入站<sup>1</sup> 和出站<sup>2</sup> 流量提供最高 50 Gbps 的带宽 |

<sup>1</sup> 发送到存储帐户的所有数据（请求）

<sup>2</sup> 从存储帐户接收的所有数据（响应）

高级页 blob 帐户是配置为高级版的通用帐户。 建议使用常规用途 v2 存储帐户。

如果对非托管磁盘使用高级页 blob 存储帐户并且应用程序超过了单个存储帐户的可伸缩性目标，则 Microsoft 建议迁移到托管磁盘。 有关托管磁盘的详细信息，请参阅[适用于 Windows vm 的 Azure 磁盘存储概述](../../virtual-machines/windows/managed-disks-overview.md)或[Linux vm Azure 磁盘存储概述](../../virtual-machines/linux/managed-disks-overview.md)。

如果无法迁移到托管磁盘，请生成应用程序以使用多个存储帐户，并在这些存储帐户中对数据进行分区。 例如，如果要将 51-TB 的磁盘附加到多个 VM，请将这些磁盘分散在两个存储帐户中。 35 TB 是单个高级存储帐户的限制。 请确保单个高级性能存储帐户的预配磁盘绝不会超过 35 TB。

## <a name="see-also"></a>另请参阅

- [标准存储帐户的可伸缩性和性能目标](../common/scalability-targets-standard-account.md)
- [高级块 blob 存储帐户的可伸缩性目标](../blobs/scalability-targets-premium-block-blobs.md)
