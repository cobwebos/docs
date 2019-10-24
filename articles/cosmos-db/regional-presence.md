---
title: Azure Cosmos DB 的区域可用性
description: 本文说明了 Azure Cosmos DB 存在的区域以及不同云环境。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753234"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Azure Cosmos DB 的区域可用性

Azure Cosmos DB 是 Azure 中的基础服务，默认情况下，在 Azure 可用的所有区域中始终可用。 目前，Azure 在全球的 [54 个地区](https://azure.microsoft.com/global-infrastructure/regions/)提供。 

[![Azure Cosmos DB 可用的区域](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB 在提供给客户的所有五种不同的 Azure 云环境中均可使用：

* Azure 公有云，全球通用。

* **Azure 中国世纪互联**是通过 Microsoft 和世纪互联，其中一个全国最大的 internet 供应商（中国）之一提供的。

* Azure 德国以数据受托人模式提供服务，这可确保客户数据仍保留在德国，由 Deutsche Telecom 的子公司 T-Systems International GmbH 充当德国数据受托人，对这些数据进行管控。

* Azure 政府在美国的四个区域向美国政府机构及其合作伙伴提供服务。 

* 适用于美国国防部的**Azure 政府版（DoD）** 在美国国防部的美国的两个区域中提供。

## <a name="regional-presence-with-global-distribution"></a>遍及各区、全球分布

默认情况下，所有 Azure 区域中都提供由 Azure Cosmos DB 公开的 Api （包括 SQL、MongoDB、Cassandra、Gremlin 和表）。 例如，你可以通过 Azure Cosmos DB 公开 MongoDB 和 Cassandra Api，这些 Api 不仅适用于所有全球 Azure 区域，而且还在主权云中，如中国、德国、政府和美国国防部（DoD）区域。

Azure Cosmos DB 是一种[全球分布式](distribute-data-globally.md)数据库服务。 可将任意数量的 Azure 区域与 Azure Cosmos 帐户相关联，并且数据会自动且透明地得到复制。 可随时向 Azure Cosmos 帐户添加或从中删除区域。 借助统包全球分布功能和多主控复制协议，Azure Cosmos DB 能够在第 99 百分位提供不到 10 毫秒的读写延迟、提供 99.999 的读写可用性并能够在与 Azure Cosmos 帐户相关的所有区域中灵活扩展预配的读写吞吐量。 Azure Cosmos DB 还提供五种定义完善的一致性模型，可以选择对数据应用特定的一致性模型。 最后，Azure Cosmos DB 是业内唯一的数据库服务，提供全面的[服务级别协议（SLA）](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) ，涵盖预配的吞吐量、99% 的延迟、高可用性和一致性。 所有 Azure 云中都提供上述功能。

## <a name="next-steps"></a>后续步骤

你现在可以通过以下文章了解 Azure Cosmos DB 的核心概念：

* [全球数据分布](distribute-data-globally.md)
* [如何管理 Azure Cosmos DB 帐户](manage-account.md)
* [为 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
