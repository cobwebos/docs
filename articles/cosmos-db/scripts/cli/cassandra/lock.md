---
title: 创建用于 Azure Cosmos DB 的 Cassandra 密钥空间和表的资源锁
description: 创建用于 Azure Cosmos DB 的 Cassandra 密钥空间和表的资源锁
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: bbb6f048f131a38ceaba244e4d8962f97e9c1431
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87429696"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>使用 Azure CLI 创建用于 Azure Cosmos Cassandra API 密钥空间和表的资源锁

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题需要运行 Azure CLI 2.9.1 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

> [!IMPORTANT]
> 对于使用任何 Cassandra SDK、CQL Shell 或 Azure 门户进行连接的用户所做的更改，除非先在启用了 `disableKeyBasedMetadataWriteAccess` 属性的情况下锁定 Cosmos DB 帐户，否则资源锁无效。 要详细了解如何启用此属性，请参阅[防止 SDK 更改](../../../role-based-access-control.md#prevent-sdk-changes)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/lock.sh "Create a resource lock for an Azure Cosmos DB Cassandra API keyspace, and table.")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | 创建锁。 |
| [az lock list](/cli/azure/lock#az-lock-list) | 列出锁信息。 |
| [az lock show](/cli/azure/lock#az-lock-show) | 显示锁的属性。 |
| [az lock delete](/cli/azure/lock#az-lock-delete) | 删除锁。 |

## <a name="next-steps"></a>后续步骤

-[锁定资源以防止意外更改](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI 文档](/cli/azure/cosmosdb)。

-[Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。
