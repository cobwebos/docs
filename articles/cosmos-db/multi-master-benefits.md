---
title: Azure Cosmos DB 多主机权益
description: 了解 Azure Cosmos DB 中的多主机的好处。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789288"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>了解 Azure Cosmos DB 中的多主优势

Cosmos DB 帐户操作员应选择合适的全局分发配置，以确保延迟、 可用性和他们的应用程序的 RTO 要求。 Azure Cosmos 帐户配置了多个写入位置具有单个写入位置包括帐户通过让用户受益匪浅，实现 99.999%编写的可用性 SLA，< 10 毫秒写入延迟 SLA 在第 99 个百分点值和 RTO = 区域性灾难中的为 0。

## <a name="comparison-of-features"></a>功能比较

|应用程序要求|多个写入位置|单个写入位置|注意|
|---|---|---|---|
|写入的延迟 SLA < 10 毫秒，级别为 P99|**是**|否|具有一个写入位置的帐户的每次写入会造成额外的跨区域网络延迟。|
|读取的延迟 SLA < 10 毫秒，级别为 P99|**是**|是| |
|编写提供 99.999%的 SLA|**是**|否|具有一个写入位置的帐户保证 99.99%的 SLA|
|RTO = 0|**是**|否|零停机时间的写入发生区域性的灾难时。 具有单个写入位置的帐户具有 15 分钟的 RTO。|

## <a name="next-steps"></a>后续步骤

如果仍想要在 Azure Cosmos 帐户中禁用 EnableMultipleWriteLocations，请[开具支持票证](https://azure.microsoft.com/support/create-ticket/)。
