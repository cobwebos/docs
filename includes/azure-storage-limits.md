---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396115"
---
下表介绍 Azure 存储的默认限制。 “传入”限制是指发送到存储帐户的所有数据（请求）。 “传出”限制是指从存储帐户接收的所有数据（响应）。

| 资源 | 默认限制 |
| --- | --- |
| 每个订阅每个区域的存储帐户数，包括标准帐户和高级帐户 | 200 |
| 最大存储帐户容量<sup>1</sup> | 500 TiB |
| 每个存储帐户的 Blob 容器、Blob、文件共享、表、队列、实体或消息数上限 | 无限制 |
| 每个存储帐户的最大请求速率<sup>1</sup> | 每秒 20,000 个请求 |
| 每个存储帐户的最大入口 <sup>1</sup>（美国区域） | 如果已启用 RA-GRS/GRS，则为 10 Gbps；对于 LRS/ZRS，为 20 Gbps<sup>2</sup> |
| 每个存储帐户的最大出口 <sup>1</sup>（美国区域） | 如果已启用 RA-GRS/GRS，则为 20 Gbps；对于 LRS/ZRS，为 30 Gbps |
| 每个存储帐户的最大入口<sup>1</sup>（非美国区域） | 如果已启用 RA-GRS/GRS，则为 5 Gbps；对于 LRS/ZRS，为 10 Gbps<sup>2</sup> |
| 每个存储帐户的最大出口<sup>1</sup>（非美国区域） | 如果已启用 RA-GRS/GRS，则为 10 Gbps；对于 LRS/ZRS，为 15 Gbps |

<sup>1</sup> Azure 存储帐户根据请求支持更高的容量、请求率、入口和出口限制。 有关增加的限制的详细信息，请参阅[宣布推出更大、更高规模的存储帐户](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。 若要请求增加帐户限制，请与 [Azure 支持](https://azure.microsoft.com/support/faq/)联系。

<sup>2</sup>[Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy)选项包括：
* **RA-GRS**：读取访问异地冗余存储。 如果已启用 RA-GRS，辅助位置的出口目标与主要位置的出口目标相同。
* **GRS**：异地冗余存储。 
* **ZRS**：区域冗余存储。
* **LRS**：本地冗余存储。 

