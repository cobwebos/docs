---
title: Azure Cosmos DB 的区域可用性
description: 本文说明了 Azure Cosmos DB 存在的区域以及不同云环境。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 787bcc8f0db60868008ec93fcacdec1283946d2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243737"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Azure Cosmos DB 的区域可用性

Azure Cosmos DB 是 Azure 中的一项基础服务，默认情况下，在所有提供 Azure 的区域均可使用。 目前，Azure 在全球的 [54 个地区](https://azure.microsoft.com/global-infrastructure/regions/)提供。 

[![Azure Cosmos DB 可用的区域](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB 在提供给客户的所有五种不同的 Azure 云环境中均可使用：

* Azure 公有云，全球通用  。

* **Azure 中国 21Vianet**可通过 Microsoft 和 21Vianet，一个国家/地区的中国最大的 internet 供应商合作。

* Azure 德国以数据受托人模式提供服务，这可确保客户数据仍保留在德国，由 Deutsche Telecom 的子公司 T-Systems International GmbH 充当德国数据受托人，对这些数据进行管控  。

* Azure 政府在美国的四个区域向美国政府机构及其合作伙伴提供服务  。 

* **美国国防部 (DoD) 适用的 azure 政府**仅在美国国防部到美国的两个区域可用。

## <a name="regional-presence-with-global-distribution"></a>遍及各区、全球分布

默认情况下的所有 Azure 区域中提供了 Azure Cosmos DB （包括 SQL、 MongoDB、 Cassandra、 Gremlin 和表） 公开的所有 Api。 例如，可以有 MongoDB 和 Cassandra Api 公开的 Azure Cosmos DB 中的所有全局 Azure 区域中，不仅在 sovereign 云中等中国、 德国、 政府和美国国防部 (DoD) 区域。

Azure Cosmos DB 是[全球分布式](distribute-data-globally.md)数据库服务。 可将任意数量的 Azure 区域与 Azure Cosmos 帐户相关联，并且数据会自动且透明地得到复制。 可随时向 Azure Cosmos 帐户添加或从中删除区域。 借助统包全球分布功能和多主控复制协议，Azure Cosmos DB 能够在第 99 百分位提供不到 10 毫秒的读写延迟、提供 99.999 的读写可用性并能够在与 Azure Cosmos 帐户相关的所有区域中灵活扩展预配的读写吞吐量。 Azure Cosmos DB 还提供五种定义完善的一致性模型，可以选择对数据应用特定的一致性模型。 最后，Azure Cosmos DB 是业内唯一一种提供综合[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) 的数据库服务，包括预配的吞吐量、第 99 百分位的延迟、高可用性和一致性。 以上功能在所有 Azure 云中推出。

## <a name="next-steps"></a>后续步骤

现可通过以下文章了解 Azure Cosmos DB 的核心概念：

* [全球数据分布](distribute-data-globally.md)
* [如何管理 Azure Cosmos DB 帐户](manage-account.md)
* [为 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
