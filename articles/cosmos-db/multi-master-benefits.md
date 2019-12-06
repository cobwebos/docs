---
title: Azure Cosmos DB 多主机优点
description: 了解多主机 Azure Cosmos DB、单个和多个写入位置中延迟和 SLA 要求之间的好处。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872003"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>了解 Azure Cosmos DB 中的多主机优点

Cosmos DB 帐户操作员应选择适当的全局分发配置，以确保应用程序的延迟、可用性和 RTO 要求。 配置为具有多个写入位置的 Azure Cosmos 帐户对于具有单个写入位置（包括99.999% 写入可用性 SLA）的帐户提供了显著的好处，而在区域灾难中，在99% 百分位和 RTO = 0 < 10 毫秒写入延迟 SLA。

## <a name="comparison-of-features"></a>功能比较

|应用程序要求|多个写入位置|单个写入位置|说明|
|---|---|---|---|
|在 P99 < 10ms 的写入延迟 SLA|**是**|No|具有单个写入位置的帐户会在每次写入时产生额外的跨区域网络延迟。|
|在 P99 上读取 < 10ms 的延迟 SLA|**是**|是| |
|写入 SLA 为99.999%|**是**|No|具有单个写入位置的帐户保证99.99% 的 SLA|
|RTO = 0|**是**|No|在发生区域性灾难的情况下，写操作时间为零。 具有单个写入位置的帐户的 RTO 为15分钟。|

## <a name="next-steps"></a>后续步骤

如果仍要禁用 Azure Cosmos 帐户中的 EnableMultipleWriteLocations，请[打开支持票证](https://azure.microsoft.com/support/create-ticket/)。
