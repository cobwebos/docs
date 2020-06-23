---
title: 为 Azure Cosmos DB Core (SQL) API 数据库和容器创建资源锁
description: 为 Azure Cosmos DB Core (SQL) API 数据库和容器创建资源锁
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 5a44376e0039e2783081785b06191f7c36c19f60
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514566"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-core-sql-api-database-and-container-using-azure-cli"></a>使用 Azure CLI 为 Azure Cosmos DB Core (SQL) API 数据库和容器创建资源锁

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题需要运行 Azure CLI 2.6.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

> [!IMPORTANT]
> 除非在启用了 `disableKeyBasedMetadataWriteAccess` 属性的情况下先锁定 Cosmos DB 帐户，否则资源锁对使用任何 Cosmos DB SDK、任何工具（通过帐户密钥连接）或 Azure 门户进行连接的用户所作的更改均无效。 要详细了解如何启用此属性，请参阅[防止 SDK 更改](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/lock.sh "Create a resource lock for an Azure Cosmos DB Core (SQL) API database and container.")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | 创建锁。 |
| [az lock list](/cli/azure/lock#az-lock-list) | 列出锁信息。 |
| [az lock show](/cli/azure/lock#az-lock-show) | 显示锁的属性。 |
| [az lock delete](/cli/azure/lock#az-lock-delete) | 删除锁。 |

## <a name="next-steps"></a>后续步骤

-[锁定资源以防止意外更改](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI 文档](/cli/azure/cosmosdb)。

-[Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。
