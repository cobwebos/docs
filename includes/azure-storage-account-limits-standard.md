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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334764"
---
下表描述了 Azure 常规用途 v1、v2、Blob 存储、块 Blob 存储以及 Data Lake Storage Gen2 启用存储帐户的默认限制。 *入口*限制是指发送到存储帐户的所有数据。 *出口*限制指从存储帐户接收的所有数据。

| 资源 | 限制 |
| --- | --- |
| 每个区域每个订阅的存储帐户数，包括标准、高级和 Data Lake Storage Gen2 启用存储帐户。<sup>3</sup> | 250 |
| 最大存储帐户容量 | 5 PiB <sup>1</sup>|
| 每个存储帐户的 Blob 容器、Blob、文件共享、表、队列、实体或消息数目上限 | 无限制 |
| 每个存储帐户的最大请求速率<sup>1</sup> | 每秒 20,000 个请求 |
| 每个存储帐户的最大入口<sup>1</sup> （美国，欧洲区域） | 25 Gbps |
| 每个存储帐户的最大入口<sup>1</sup> （美国和欧洲区域除外） | 如果启用了 GRS/GRS，则为 5 gbps; 对于 LRS/ZRS<sup>2</sup>为 10 gbps |
| 常规用途 v2 存储帐户和 Blob 存储帐户的最大出口（所有区域） | 50 Gbps |
| 常规用途 v1 存储帐户的最大出口（美国区域） | 如果启用了 GRS/GRS，则为 20 gbps; 对于 LRS/ZRS<sup>2</sup> ，则为 30 gbps |
| 常规用途 v1 存储帐户的最大出口（非美国区域） | 如果启用了 GRS/GRS，则为 10 gbps; 对于 LRS/ZRS<sup>2</sup> ，则为 15 gbps |
| 每个存储帐户的最大虚拟网络规则数 | 200 |
| 每个存储帐户的最大 IP 地址规则数 | 200 |

<sup>1</sup> Azure 存储标准帐户支持更高的容量限制和更高的请求入口限制。 若要请求增加帐户限制，请与 [Azure 支持](https://azure.microsoft.com/support/faq/)联系。

<sup>2</sup>如果你的存储帐户使用异地冗余存储（GRS）或地理区域冗余存储（GZRS）启用了读取访问，则辅助位置的出口目标与主位置相同。 [Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy)选项包括：

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md)是一组专门用于在 Azure Blob 存储基础上进行大数据分析的功能。 Azure 存储和 blob 存储限制适用于 Data Lake Storage Gen2。

> [!NOTE]
> 在大多数情况下，Microsoft 建议使用常规用途 v2 存储帐户。 可以轻松将常规用途 v1 或 Azure Blob 存储帐户升级到常规用途 v2 帐户，无需停机且无需复制数据。 有关详细信息，请参阅[升级到常规用途 v2 存储帐户](../articles/storage/common/storage-account-upgrade.md)。

如果应用程序的需求超过单个存储帐户的伸缩性目标，则可以构建使用多个存储帐户的应用程序。 然后，可以将数据对象分布到这些存储帐户中。 有关批量定价的信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

所有存储帐户都在平面网络拓扑上运行，而不考虑它们的创建时间。 有关 Azure 存储的扁平网络体系结构和可伸缩性的详细信息，请参阅 [Windows Azure 存储：具有高度一致性的高可用云存储服务](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)。 
