---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c908b7f72e5581122427a7d711ae9cf61d2d90f
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665579"
---
下表描述了 Azure 常规用途 v1、v2、Blob 存储和块 blob 存储帐户的默认限制。 “流入量”限制是指针对发送到存储帐户的所有数据的限制。 “流出量”限制是指针对从存储帐户接收的所有数据的限制。

> [!NOTE]
> 你可以请求更高的容量和流入量限制。 要请求提高限制，请联系 [Azure 支持](https://azure.microsoft.com/support/faq/)。

| 资源 | 限制 |
| --- | --- |
| 每个订阅每个区域的存储帐户数，包括标准存储帐户和高级存储帐户。| 250 |
| 最大存储帐户容量 | 5 PiB <sup>1</sup>|
| 每个存储帐户的 Blob 容器、Blob、文件共享、表、队列、实体或消息数目上限 | 无限制 |
| 每个存储帐户的最大请求速率<sup>1</sup> | 每秒 20,000 个请求 |
| 每个存储帐户的最大流入量<sup>1</sup>（美国、欧洲区域） | 10 Gbps |
| 每个存储帐户的最大流入量<sup>1</sup>（美国和欧洲之外的区域） | 如果已启用 RA-GRS/GRS，则为 5 Gbps；对于 LRS/ZRS，为 10 Gbps<sup>2</sup> |
| 常规用途 v2 存储帐户和 Blob 存储帐户的最大流出量（所有区域） | 50 Gbps |
| 常规用途 v1 存储帐户的最大流出量（美国区域） | 如果已启用 RA-GRS/GRS，则为 20 Gbps；对于 LRS/ZRS，为 30 Gbps<sup>2</sup> |
| 常规用途 v1 存储帐户的最大流出量（非美国区域） | 如果已启用 RA-GRS/GRS，则为 10 Gbps；对于 LRS/ZRS，为 15 Gbps<sup>2</sup> |
| 每个存储帐户的虚拟网络规则数目上限 | 200 |
| 每个存储帐户的最大 IP 地址规则数 | 200 |

<sup>1</sup> Azure 存储标准帐户根据请求支持更高的容量上限和更高的流入量上限。 若要请求增加帐户限制，请与 [Azure 支持](https://azure.microsoft.com/support/faq/)联系。

<sup>2</sup> 如果存储帐户具有启用了读取访问权限的异地冗余存储 (RA-GRS) 或异地区域冗余存储 (RA-GZRS)，则辅助位置的流出量目标与主位置的流出量目标相同。 有关详细信息，请参阅 [Azure 存储复制](../articles/storage/common/storage-redundancy.md)。

> [!NOTE]
> Microsoft 建议对大多数方案使用常规用途 v2 存储帐户。 可以轻松将常规用途 v1 或 Azure Blob 存储帐户升级到常规用途 v2 帐户，无需停机且无需复制数据。 有关详细信息，请参阅[升级到常规用途 v2 存储帐户](../articles/storage/common/storage-account-upgrade.md)。

所有存储帐户都在平面网络拓扑上运行，而无论它们是何时创建的。 有关 Azure 存储的扁平网络体系结构和可伸缩性的详细信息，请参阅 [Microsoft Azure 存储：具有非常一致性的高可用云存储服务](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)。 

