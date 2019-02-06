---
title: 使用 Azure CLI 管理 Azure Cosmos DB 资源
description: 使用 Azure CLI 管理 Azure Cosmos DB 帐户、数据库和容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: c3028fd18bd9afefaa18f7f515a43a852ddef78a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464392"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>使用 Azure CLI 管理 Azure Cosmos 资源

以下指南介绍了使用 Azure CLI 自动管理 Azure Cosmos DB 帐户、数据库和容器的命令。 它还包括用来缩放容器吞吐量的命令。 [Azure CLI 参考](https://docs.microsoft.com/cli/azure/cosmosdb)中收录了所有 Azure Cosmos DB CLI 命令的参考页。 还可以在[针对 Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)中找到更多示例，包括如何为 MongoDB、Gremlin、Cassandra 和表 API 创建和管理 Cosmos DB 帐户、数据库和容器。

此示例 CLI 脚本创建一个 Azure Cosmos DB SQL API 帐户、数据库和容器。  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

若要在“美国东部”和“美国西部”中创建启用了 SQL API、会话一致性和多主的 Azure Cosmos DB 帐户，请打开 Azure CLI 或 Cloud Shell，并运行以下命令：

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>创建数据库

若要创建 Cosmos DB 数据库，请打开 Azure CLI 或 Cloud Shell，并运行以下命令：

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>创建容器

若要创建吞吐量为 1000 RU/秒且具有分区键的 Cosmos DB 容器，请打开 Azure CLI 或 Cloud Shell，并运行以下命令：

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>更改容器的吞吐量

若要将 Cosmos DB 容器的吞吐量更改为 400 RU/秒，请打开 Azure CLI 或 Cloud Shell，并运行以下命令：

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>列出帐户密钥

创建 Azure Cosmos DB 帐户时，服务生成两个主访问密钥，可用于访问 Azure Cosmos DB 帐户时的身份验证。 提供两个访问密钥后，Azure Cosmos DB 支持在不中断 Azure Cosmos DB 帐户连接的情况下重新生成密钥。 还可以使用只读密钥，用于对只读操作进行身份验证。 有两个读写密钥（主密钥和辅助密钥）和两个只读密钥（主密钥和辅助密钥）。 可以通过运行以下命令来获取帐户的密钥：

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>列出连接字符串

可以使用以下命令检索用于将应用连接到 Cosmos DB 帐户的连接字符串。

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>重新生成帐户密钥

应定期更改 Azure Cosmos DB 帐户访问密钥，使连接更安全。 将分配两个访问密钥，从而可以在使用一个访问密钥保持连接到 Azure Cosmos DB 帐户的同时，再生成另一个访问密钥。

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅：

- [安装 Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI 参考](https://docs.microsoft.com/cli/azure/cosmosdb)
- [针对 Azure Cosmos DB 的其他 Azure CLI 示例](cli-samples.md)
