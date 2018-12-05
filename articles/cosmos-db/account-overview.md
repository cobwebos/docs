---
title: 使用 Azure Cosmos DB 帐户
description: 本文介绍如何创建和使用 Azure Cosmos DB 帐户
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: e73c295c214728bf07b65ae4402c3b5abe21932f
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165282"
---
# <a name="working-with-azure-cosmos-account"></a>使用 Azure Cosmos 帐户

Azure Cosmos DB 是完全托管的平台即服务 (PaaS)。 若要开始使用 Azure Cosmos DB，首先应在 Azure 订阅中创建一个 Azure Cosmos 帐户。 Azure Cosmos 帐户包含唯一的 DNS 名称，可以使用 Azure 门户、Azure CLI 或不同的特定于语言的 SDK 来管理帐户。 有关详细信息，请参阅[如何管理 Azure Cosmos 帐户](how-to-manage-database-account.md)。

Azure Cosmos 帐户是全局分配和高可用性的基本单元。 要在多个 Azure 区域之间全局分配数据和吞吐量，随时可在 Azure Cosmos 帐户中添加和删除 Azure 区域。 可将 Azure Cosmos 帐户配置为使用一个或多个写入区域。 有关详细信息，请参阅[如何在 Azure Cosmos 帐户中添加和删除 Azure 区域](how-to-manage-database-account.md)。 可以在 Azure Cosmos 帐户中配置[默认一致性](consistency-levels.md)级别。 Azure Cosmos DB 提供综合性的 SLA，涵盖了吞吐量、99% 时间内的延迟、一致性和高可用性。 有关详细信息，请参阅 [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)。

若要安全管理对 Azure Cosmos 帐户中所有数据的访问，可以使用与帐户关联的主密钥。 若要进一步保护对数据的访问，可在 Azure Cosmos 帐户中配置 VNET 服务终结点和 IP 防火墙。 

## <a name="elements-in-an-azure-cosmos-account"></a>Azure Cosmos 帐户中的元素

Azure Cosmos DB 容器是基本的缩放单元。 容器可以提供几乎无限的预配吞吐量 (RU/s) 和存储。 Azure Cosmos DB 使用指定的逻辑分区键以透明方式将容器分区，以弹性缩放预配吞吐量和存储。 有关详细信息，请参阅[使用 Azure Cosmos 容器和项](databases-containers-items.md)。

目前，在一个 Azure 订阅下最多可以创建 100 个 Azure Cosmos 帐户。 单个 Azure Cosmos 帐户几乎可以管理无限量的数据和预配吞吐量。 若要管理数据和预配吞吐量，可以在帐户下创建一个或多个 Azure Cosmos 数据库，而在该数据库中，可以创建一个或多个容器。 下图显示了 Azure Cosmos 帐户中的元素层次结构：

![Azure Cosmos 帐户的层次结构](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>后续步骤

接下来，可以继续了解如何管理 Azure Cosmos 帐户，或参阅与 Azure Cosmos DB 相关的其他概念：

* [如何管理 Azure Cosmos 帐户](how-to-manage-database-account.md)
* [全局分发](distribute-data-globally.md)
* [一致性级别](consistency-levels.md)
* [使用 Azure Cosmos 容器和项](databases-containers-items.md)
* [Azure Cosmos 帐户的 VNET 服务终结点](firewall-support.md)
* [Azure Cosmos 帐户的 IP 防火墙](vnet-service-endpoint.md)
* [如何在 Azure Cosmos 帐户中添加和删除 Azure 区域](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
