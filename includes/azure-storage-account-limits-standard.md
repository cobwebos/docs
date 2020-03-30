---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334764"
---
下表描述了 Azure 通用 v1、v2、Blob 存储、块 Blob 存储和启用数据存储库的存储帐户的默认限制。 *入口*限制是指发送到存储帐户的所有数据。 *出口*限制是指从存储帐户接收的所有数据。

| 资源 | 限制 |
| --- | --- |
| 每个订阅每个区域的存储帐户数，包括启用标准存储 Gen2 的标准存储帐户和数据存储库帐户。<sup>3</sup> | 250 |
| 最大存储帐户容量 | 5 PiB <sup>1</sup>|
| 每个存储帐户的 Blob 容器、Blob、文件共享、表、队列、实体或消息数上限 | 无限制 |
| 每个存储帐户的最大请求速率<sup>1</sup> | 每秒 20,000 个请求 |
| 每个存储帐户的最大入口<sup>1（</sup>美国、欧洲区域） | 25 Gbps |
| 每个存储帐户的最大入口<sup>1（</sup>美国和欧洲以外的区域） | 启用 RA-GRS/GRS 时为 5 Gbps，LRS/ZRS<sup>2</sup>为 10 Gbps |
| 常规用途 v2 存储帐户和 Blob 存储帐户的最大出口（所有区域） | 50 Gbps |
| 通用 v1 存储帐户的最大出口（美国区域） | 如果启用 RA-GRS/GRS，则为 20 Gbps，LRS/ZRS<sup>2</sup>的 30 Gbps |
| 常规用途 v1 存储帐户的最大出口（非美国区域） | 如果启用 RA-GRS/GRS，则为 10 Gbps，LRS/ZRS<sup>2</sup>的 15 Gbps |
| 每个存储帐户的最大虚拟网络规则数 | 200 |
| 每个存储帐户的最大 IP 地址规则数 | 200 |

<sup>1</sup> Azure 存储标准帐户支持更高的容量限制和更高的请求进入限制。 若要请求增加帐户限制，请与 [Azure 支持](https://azure.microsoft.com/support/faq/)联系。

<sup>2</sup>如果您的存储帐户启用了具有异地冗余存储 （RA-GRS） 或地理区域冗余存储 （RA-GZRS） 的读取访问，则辅助位置的传出目标与主位置的传出目标相同。 [Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy)选项包括：

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure 数据湖存储 Gen2](../articles/storage/blobs/data-lake-storage-introduction.md)是一组专用于 Azure Blob 存储的大型数据分析功能。 Azure 存储和 Blob 存储限制适用于数据湖存储第 2 代。

> [!NOTE]
> Microsoft 建议您在大多数方案中使用通用 v2 存储帐户。 可以轻松将常规用途 v1 或 Azure Blob 存储帐户升级到常规用途 v2 帐户，无需停机且无需复制数据。 有关详细信息，请参阅[升级到常规用途 v2 存储帐户](../articles/storage/common/storage-account-upgrade.md)。

如果应用程序的需求超过单个存储帐户的伸缩性目标，则可以构建使用多个存储帐户的应用程序。 然后，可以将数据对象分布到这些存储帐户中。 有关批量定价的信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

所有存储帐户都运行在平面网络拓扑上，无论创建时间如何。 有关 Azure 存储的扁平网络体系结构和可伸缩性的详细信息，请参阅 [Windows Azure 存储：具有高度一致性的高可用云存储服务](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)。 
