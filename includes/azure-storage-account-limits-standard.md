---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/27/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e02de4ee9c36f9c3f0c27bc02ee1c37ce3e4057c
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78208246"
---
下表描述了 Azure 常规用途 v1、v2 和 Blob 存储帐户的默认限制。 *入口*限制是指发送到存储帐户的所有数据。 *出口*限制指从存储帐户接收的所有数据。

| 资源 | 默认限制 |
| --- | --- |
| 每个订阅每个区域的存储帐户数，包括标准帐户和高级帐户 | 250 |
| 最大存储帐户容量 | 5 PiB <sup>1</sup>|
| 每个存储帐户的 blob 容器、blob、文件共享、表、队列、实体或消息的最大数目 | 无限制 |
| 每个存储帐户的最大请求速率<sup>1</sup> | 每秒 20,000 个请求 |
| 每个存储帐户的最大入口<sup>1</sup> （美国，欧洲区域） | 25 Gbps |
| 每个存储帐户的最大入口<sup>1</sup> （美国和欧洲区域除外） | 如果启用了 GRS/GRS，则为 5 gbps; 对于 LRS/ZRS<sup>2</sup>为 10 gbps |
| 常规用途 v2 和 Blob 存储帐户的最大出口（所有区域） | 50 Gbps |
| 常规用途 v1 存储帐户的最大出口（美国区域） | 如果启用了 GRS/GRS，则为 20 gbps; 对于 LRS/ZRS<sup>2</sup> ，则为 30 gbps |
| 常规用途 v1 存储帐户的最大出口（非美国区域） | 如果启用了 GRS/GRS，则为 10 gbps; 对于 LRS/ZRS<sup>2</sup> ，则为 15 gbps |
| 每个存储帐户的最大虚拟网络规则数 | 200 |
| 每个存储帐户的最大 IP 地址规则数 | 200 |

<sup>1</sup>Azure 存储标准帐户支持更高的容量限制和更高的请求入口限制。 若要请求提高帐户入口上限，请与 [Azure 支持](https://azure.microsoft.com/support/faq/)联系。

<sup>2</sup>如果你的存储帐户使用异地冗余存储（GRS）或地理区域冗余存储（GZRS）启用了读取访问，则辅助位置的出口目标与主位置相同。 [Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy)选项包括：

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> 在大多数情况下，Microsoft 建议使用常规用途 v2 存储帐户。 可以轻松地将常规用途 v1 或 Azure Blob 存储帐户升级为常规用途 v2 帐户，无需停机，也无需复制数据。 有关详细信息，请参阅[升级到常规用途 v2 存储帐户](../articles/storage/common/storage-account-upgrade.md)。

如果应用程序的需求超过单个存储帐户的伸缩性目标，则可以构建使用多个存储帐户的应用程序。 然后，可以将数据对象分布到这些存储帐户中。 有关卷定价的信息，请参阅[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

所有存储帐户都在扁平网络拓扑上运行，无论它们创建于何时，都支持本文所述的可伸缩性和性能目标。 有关 Azure 存储的扁平网络体系结构和可伸缩性的详细信息，请参阅 [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)（Microsoft Azure 存储：具有高度一致性的高可用云存储服务）。
