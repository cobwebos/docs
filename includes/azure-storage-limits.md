---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 01d84914682d40b97c3d480a753c8b966cf61acc
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553304"
---
下表介绍 Azure 存储的默认限制。 *入口*限制指发送到存储帐户的请求中的所有数据。 *出口*限制指从存储帐户接收的响应中的所有数据。

| 资源 | 默认限制 |
| --- | --- |
| 每个区域每个订阅，其中包括标准和高级帐户的存储帐户数 | 250 |
| 最大存储帐户容量 | 2 PB 针对美国和欧洲，对于所有其他区域，其中包括英国 500 TB |
| 最大数目的 blob 容器、 blob、 文件共享、 表、 队列、 实体或每个存储帐户的消息 | 无限制 |
| 每个存储帐户的最大请求速率<sup>1</sup> | 每秒 20,000 个请求 |
| 最大入口<sup>1</sup>每个存储帐户 （美国区域） | 如果已启用 RA-GRS/GRS 的 10 Gbps，20 Gbps; 对于 LRS/ZRS<sup>2</sup> |
| 最大入口<sup>1</sup>每个存储帐户 （非美国区域） | 如果已启用 RA-GRS/GRS，则为 5 Gbps，10 Gbps; 对于 LRS/ZRS<sup>2</sup> |
| 对于常规用途 v2 和 Blob 存储帐户 （所有区域） 的最大出口 | 50 Gbps |
| 常规用途 v1 存储帐户 （美国区域） 的最大出口 | 如果启用 RA-GRS/GRS，则为 20 Gbps、 30 Gbps; 对于 LRS/ZRS<sup>2</sup> |
| 常规用途 v1 存储帐户 （非美国区域） 的最大出口 | 如果已启用 RA-GRS/GRS 的 10 Gbps，15 Gbps; 对于 LRS/ZRS<sup>2</sup> |

<sup>1</sup>azure 标准存储帐户支持更高的限制为入口的请求。 若要请求提高帐户入口上限，请与 [Azure 支持](https://azure.microsoft.com/support/faq/)联系。

<sup>2</sup> [Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy)选项包括：
* **RA-GRS**：读取访问异地冗余存储。 如果已启用 RA-GRS，辅助位置的出口目标与主要位置的出口目标相同。
* **GRS**：异地冗余存储。 
* **ZRS**：区域冗余存储。
* **LRS**：本地冗余存储。 

> [!NOTE]
> 我们建议您在大多数情况下使用常规用途 v2 存储帐户。 可以轻松升级的常规用途 v2 帐户而无需停机，而无需将数据复制到常规用途 v1 或 Azure Blob 存储帐户。
>
> 有关 Azure 存储帐户的详细信息，请参阅[存储帐户概述](../articles/storage/common/storage-account-overview.md)。 

如果应用程序的需求超过单个存储帐户的伸缩性目标，则可以构建使用多个存储帐户的应用程序。 然后，可以将数据对象分布到这些存储帐户中。 有关批量定价的信息，请参阅[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

所有存储帐户都在扁平网络拓扑上运行，无论它们创建于何时，都支持本文所述的可伸缩性和性能目标。 有关 Azure 存储的扁平网络体系结构和可伸缩性的详细信息，请参阅 [Microsoft Azure 存储：具有非常一致性的高可用云存储服务](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。

