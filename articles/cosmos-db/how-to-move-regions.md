---
title: 了解如何将 Azure Cosmos DB 帐户移到另一个区域
description: 了解如何将 Azure Cosmos DB 帐户移到另一个区域
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059185"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>如何将 Azure Cosmos DB 帐户移到另一个区域

本文介绍了如何将数据复制到 Azure Cosmos DB 中，或者如何将帐户迁移 (Azure 资源管理器) 元数据以及数据从一个区域迁移到另一个区域。

## <a name="move-data-from-one-region-to-another"></a>将数据从一个区域移到另一个区域

Azure Cosmos DB 在本机上支持数据复制，因此，将数据从一个区域移到另一个区域非常简单，可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 完成此操作，包括以下步骤：

1. 将新区域添加到帐户

    若要将新区域添加到 Azure Cosmos DB 帐户，请参阅在 [Azure Cosmos DB 帐户中添加/删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

1. 执行手动故障转移到新的区域

    如果要删除的区域目前是帐户的写入区域，则需要启动到上面添加的新区域的故障转移。 这是零停机操作。 如果要移动多区域帐户中的读取区域，则可以跳过此步骤。 若要启动故障转移，请参阅 [在 Azure Cosmos 帐户上执行手动故障转移](how-to-manage-database-account.md#manual-failover)

1. 删除原始区域

    若要从 Azure Cosmos DB 帐户中删除某个区域，请参阅在 [Azure Cosmos DB 帐户中添加/删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>迁移 Azure Cosmos DB 帐户元数据

Azure Cosmos DB 不支持将帐户元数据从一个区域迁移到另一个区域。 若要将帐户元数据和客户数据从一个区域迁移到另一个区域，必须在所需的区域中创建新帐户，然后手动复制数据。 SQL API 接近零停机时间的迁移需要使用 [ChangeFeed](change-feed.md) 或利用它的工具。 如果迁移 MongoDB API、Cassandra 或其他 API 或了解有关在帐户之间迁移数据时选项的详细信息，请参阅 [用于将本地数据或云数据迁移到 Azure Cosmos DB 的选项](cosmosdb-migrationchoices.md)。 下面的步骤演示如何将 SQL API 及其数据的 Azure Cosmos DB 帐户从一个区域迁移到另一个区域：

1. 在所需的区域中创建新的 Azure Cosmos DB 帐户

    若要通过 Azure 门户、PowerShell 或 CLI 创建新帐户，请参阅 [创建 Azure Cosmos DB 帐户](how-to-manage-database-account.md#create-an-account)。

1. 创建新的数据库和容器

    若要创建新的数据库和容器，请参阅 [创建 Azure Cosmos 容器](how-to-create-container.md)

1. 利用 Azure Cosmos DB 实时数据迁移工具迁移数据

    若要在接近零停机时间的情况迁移数据，请参阅 [Azure Cosmos DB 实时数据迁移工具](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. 更新应用程序连接字符串

    当实时迁移程序工具仍在运行时，请在应用程序的新部署中更新连接信息。 可以从 Azure 门户检索应用程序的终结点和密钥。

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure 门户中的访问控制 (IAM) - 演示 NoSQL 数据库安全性":::

1. 将请求重定向到新应用程序

    新应用程序连接到之后 Azure Cosmos DB 你可以将客户端请求重定向到新的部署。

1. 删除不再需要的任何资源

    现在，请求会完全重定向到新实例，然后可以删除旧的 Azure Cosmos DB 帐户和实时数据迁移工具。

## <a name="next-steps"></a>后续步骤

有关如何管理 Azure Cosmos 帐户以及数据库和容器的详细信息和示例，请阅读以下文章：

* [管理 Azure Cosmos 帐户](how-to-manage-database-account.md)
* [Azure Cosmos DB 中的更改源](change-feed.md)
