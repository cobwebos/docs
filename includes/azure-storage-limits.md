---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/23/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 84333b26ac70db4b400f7236d4255f4b57a6ca7d
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571846"
---
下表介绍 Azure 存储的默认限制。 “传入”限制是指发送到存储帐户的所有数据（请求）。 “传出”限制是指从存储帐户接收的所有数据（响应）。

| 资源 | 默认限制 |
| --- | --- |
| 每个订阅每个区域的存储帐户数，包括标准帐户和高级帐户 | 250 |
| 最大存储帐户容量 | 对于美国和欧洲为 2 PB，对于包括英国在内的其他所有区域为 500 TB |
| 每个存储帐户的 Blob 容器、Blob、文件共享、表、队列、实体或消息数上限 | 无限制 |
| 每个存储帐户的最大请求速率<sup>1</sup> | 每秒 20,000 个请求 |
| 每个存储帐户的最大入口 <sup>1</sup>（美国区域） | 如果已启用 RA-GRS/GRS，则为 10 Gbps；对于 LRS/ZRS，为 20 Gbps<sup>2</sup> |
| 每个存储帐户的最大入口<sup>1</sup>（非美国区域） | 如果已启用 RA-GRS/GRS，则为 5 Gbps；对于 LRS/ZRS，为 10 Gbps<sup>2</sup> |
| 常规用途 v2 存储帐户和 Blob 存储帐户的最大出口（所有区域） | 50 Gbps |
| 常规用途 v1 存储帐户的最大出口（美国区域） | 如果已启用 RA-GRS/GRS，则为 20 Gbps；对于 LRS/ZRS <sup>2</sup>，为 30 Gbps |
| 常规用途 v1 存储帐户的最大出口（非美国区域） | 如果已启用 RA-GRS/GRS，则为 10 Gbps；对于 LRS/ZRS <sup>2</sup>，为 15 Gbps |

<sup>1</sup> Azure 存储帐户根据请求支持更高的入口上限。 若要请求提高帐户入口上限，请与 [Azure 支持](https://azure.microsoft.com/support/faq/)联系。

<sup>2</sup>[Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy)选项包括：
* **RA-GRS**：读取访问异地冗余存储。 如果已启用 RA-GRS，辅助位置的出口目标与主要位置的出口目标相同。
* **GRS**：异地冗余存储。 
* **ZRS**：区域冗余存储。
* **LRS**：本地冗余存储。 

如果应用程序的需求超过单个存储帐户的伸缩性目标，则可以构建使用多个存储帐户的应用程序。 然后，可以将数据对象分布到这些存储帐户中。 有关批量定价的信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/) 。

所有存储帐户都在扁平网络拓扑上运行，无论它们创建于何时，都支持本文所述的可伸缩性和性能目标。 有关 Azure 存储的扁平网络体系结构和可伸缩性的详细信息，请参阅 [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)（Microsoft Azure 存储：具有高度一致性的高可用云存储服务）。

