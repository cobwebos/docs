---
title: 将 Azure Cosmos DB 帐户移到另一个区域
description: 了解如何将 Azure Cosmos DB 帐户移到另一个区域。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: b34bc81f48b806b1016fbbd19d3ebc8bfef908c2
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090527"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>将 Azure Cosmos DB 帐户移到另一个区域

本文介绍如何执行以下操作之一：

- 在 Azure Cosmos DB 中将数据复制到其中的区域。
- 将帐户 (Azure 资源管理器) 元数据和数据从一个区域迁移到另一个区域。

## <a name="move-data-from-one-region-to-another"></a>将数据从一个区域移到另一个区域

Azure Cosmos DB 支持本机数据复制，因此，将数据从一个区域移到另一个区域非常简单。 您可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 实现此目的。 它涉及以下步骤：

1. 将新区域添加到帐户。

    若要将新区域添加到 Azure Cosmos DB 帐户，请参阅向 [Azure Cosmos DB 帐户中添加/删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。

1. 执行手动故障转移到新的区域。

    当要删除的区域当前为帐户的写入区域时，你将需要开始故障转移到上一步骤中添加的新区域。 这是一项零停机时间。 如果要移动多区域帐户中的读取区域，则可以跳过此步骤。 
    
    若要启动故障转移，请参阅 [在 Azure Cosmos 帐户上执行手动故障转移](how-to-manage-database-account.md#manual-failover)。

1. 删除原始区域。

    若要从 Azure Cosmos DB 帐户中删除某个区域，请参阅 [从你的 Azure Cosmos DB 帐户中添加/删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。

## <a name="migrate-azure-cosmos-db-account-metadata"></a>迁移 Azure Cosmos DB 帐户元数据

Azure Cosmos DB 不能以本机方式支持将帐户元数据从一个区域迁移到另一个区域。 若要将帐户元数据和客户数据从一个区域迁移到另一个区域，必须在所需的区域中创建新帐户，然后手动复制数据。 

SQL API 的近零停机时间迁移需要使用 [更改源](change-feed.md) 或使用它的工具。 如果要迁移 MongoDB API、Cassandra API 或其他 API，或了解有关在帐户之间迁移数据的选项的详细信息，请参阅 [将本地或云数据迁移到 Azure Cosmos DB 的选项](cosmosdb-migrationchoices.md)。 

以下步骤演示了如何将 SQL API 及其数据的 Azure Cosmos DB 帐户从一个区域迁移到另一个区域：

1. 在所需的区域中创建新的 Azure Cosmos DB 帐户。

    若要通过 Azure 门户、PowerShell 或 Azure CLI 创建新帐户，请参阅 [创建 Azure Cosmos DB 帐户](how-to-manage-database-account.md#create-an-account)。

1. 创建新的数据库和容器。

    若要创建新的数据库和容器，请参阅 [创建 Azure Cosmos 容器](how-to-create-container.md)。

1. 使用 Azure Cosmos DB 实时数据迁移程序工具迁移数据。

    若要在接近零停机时间的情况下迁移数据，请参阅 [Azure Cosmos DB 实时数据迁移工具](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)。

1. 更新应用程序连接字符串。

    当实时数据迁移程序工具仍在运行时，请在应用程序的新部署中更新连接信息。 可以从 Azure 门户检索应用程序的终结点和密钥。

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure 门户中的访问控制，演示 NoSQL 数据库安全性。":::

1. 将请求重定向到新应用程序。

    新应用程序连接到 Azure Cosmos DB 后，可以将客户端请求重定向到新的部署。

1. 删除不再需要的所有资源。

    现在，请求会完全重定向到新的实例，你可以删除旧的 Azure Cosmos DB 帐户和实时数据迁移工具。

## <a name="next-steps"></a>后续步骤

有关如何管理 Azure Cosmos 帐户以及数据库和容器的详细信息和示例，请阅读以下文章：

* [管理 Azure Cosmos 帐户](how-to-manage-database-account.md)
* [Azure Cosmos DB 中的更改源](change-feed.md)
