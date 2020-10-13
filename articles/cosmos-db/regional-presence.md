---
title: Azure Cosmos DB 的区域可用性
description: 本文说明了 Azure Cosmos DB 存在的区域以及不同云环境。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: e2242e8febad5d55813721266f1286250af47111
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082879"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Azure Cosmos DB 的区域可用性

Azure Cosmos DB 是 Azure 中的一项基础服务，默认情况下，在[所有提供 Azure 的区域](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)均可使用。

:::image type="content" source="./media/regional-presence/regional-presence.png" alt-text="Azure Cosmos DB 可用的区域" lightbox="./media/regional-presence/regional-presence.png" border="false":::

Cosmos DB 在提供给客户的所有五种不同的 Azure 云环境中均可使用：

* Azure 公有云，全球通用。

* **Azure 中国世纪互联** 是通过 Microsoft 和世纪互联，其中一个全国最大的 internet 供应商（中国）之一提供的。

* **Azure 德国** 在数据受信者模型下提供服务，这可确保客户数据保留在德国的 T 系统国际 GmbH （德国 Telekom 的子公司）控制下，作为德国数据受信者。

* Azure 政府在美国的四个区域向美国政府机构及其合作伙伴提供服务。 

* 适用于美国国防部的**Azure 政府部门 (DoD) **在美国到美国国防部的两个区域中提供。

## <a name="regional-presence-with-global-distribution"></a>遍及各区、全球分布

默认情况下，所有 Azure 区域中都提供了 Azure Cosmos DB (公开的所有 Api，包括 SQL、MongoDB、Cassandra、Gremlin 和表) 。 例如，你可以通过 Azure Cosmos DB 公开 MongoDB 和 Cassandra Api，这些 Api 不仅在所有全球 Azure 区域中提供，还可以在主权云中（如中国、德国、政府和美国国防部） (DoD) 区域。

Azure Cosmos DB 是一种 [全球分布式](distribute-data-globally.md) 数据库服务。 可将任意数量的 Azure 区域与 Azure Cosmos 帐户相关联，并且数据会自动且透明地得到复制。 可随时向 Azure Cosmos 帐户添加或从中删除区域。 借助统包全球分布功能和多主控复制协议，Azure Cosmos DB 能够在第 99 百分位提供不到 10 毫秒的读写延迟、提供 99.999 的读写可用性并能够在与 Azure Cosmos 帐户相关的所有区域中灵活扩展预配的读写吞吐量。 Azure Cosmos DB 还提供五种定义完善的一致性模型，可以选择对数据应用特定的一致性模型。 最后，Azure Cosmos DB 是业内唯一一种提供综合[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) 的数据库服务，包括预配的吞吐量、第 99 百分位的延迟、高可用性和一致性。 以上功能在所有 Azure 云中推出。

全局复制支持范围在 Azure 云环境内。 例如， *azure 公共* 中的 Azure Cosmos DB 帐户可以复制到 *azure* 中的任何 azure 区域，但不能复制到 *azure 中国世纪互联*的 azure 区域。

## <a name="next-steps"></a>后续步骤

现可通过以下文章了解 Azure Cosmos DB 的核心概念：

* [全球数据分布](distribute-data-globally.md)
* [如何管理 Azure Cosmos DB 帐户](manage-account.md)
* [为 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
