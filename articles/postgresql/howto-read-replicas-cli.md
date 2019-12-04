---
title: 管理读取副本-Azure CLI、REST API Azure Database for PostgreSQL-单服务器
description: 了解如何从 Azure CLI 和 REST API 管理 Azure Database for PostgreSQL 单服务器中的读取副本
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: fb0803987428ced688e83a37fae36c61b63a28a8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770112"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>从 Azure CLI 创建和管理读取副本，REST API

本文介绍如何使用 Azure CLI 和 REST API 在 Azure Database for PostgreSQL 中创建和管理读取副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

## <a name="azure-cli"></a>Azure CLI
您可以使用 Azure CLI 创建和管理读取副本。

### <a name="prerequisites"></a>必备组件

- [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 用作主服务器的 [Azure Database for PostgreSQL 服务器](quickstart-create-server-up-azure-cli.md)。


### <a name="prepare-the-master-server"></a>准备主服务器
必须使用以下步骤在“常规用途”和“内存优化”层中准备主服务器。

主服务器上的 `azure.replication_support` 参数必须设置为 **REPLICA**。 更改此静态参数时，需要重新启动服务器才能使更改生效。

1. 将 `azure.replication_support` 设置为副本。

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. 重新启动服务器以应用更改。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>创建只读副本

[Az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create)命令需要以下参数：

| 设置 | 示例值 | 描述  |
| --- | --- | --- |
| resource-group | myresourcegroup |  将在其中创建副本服务器的资源组。  |
| name | mydemoserver-副本 | 所创建的新副本服务器的名称。 |
| source-server | mydemoserver | 要从中进行复制的现有主服务器的名称或资源 ID。 |

在下面的 CLI 示例中，将在与 master 相同的区域中创建副本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

若要创建跨区域读取副本，请使用 `--location` 参数。 下面的 CLI 示例在美国西部创建副本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要了解有关可以在中创建副本的区域的详细信息，请访问[读取副本概念一文](concepts-read-replicas.md)。 

如果未在常规用途或内存优化的主服务器上将 `azure.replication_support` 参数设置为**副本**，然后重新启动服务器，则会收到错误。 在创建副本之前完成这两个步骤。

使用与 master 相同的计算和存储设置创建副本。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。

> [!IMPORTANT]
> 将主服务器设置更新为新值之前，请将副本设置更新为一个相等或更大的值。 此操作可帮助副本保持对主副本所做的任何更改。

### <a name="list-replicas"></a>列出副本
您可以使用[az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)命令查看主服务器的副本列表。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
您可以使用[az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)命令停止主服务器和读取副本之间的复制。

停止复制到主服务器和只读副本后，无法撤消该操作。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>删除主服务器或副本服务器
若要删除主服务器或副本服务器，请使用[az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)命令。

删除主服务器后，将停止复制到所有只读副本。 只读副本将成为支持读取和写入的独立服务器。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
可以使用[Azure REST API](/rest/api/azure/)创建和管理读取副本。

### <a name="prepare-the-master-server"></a>准备主服务器
必须使用以下步骤在“常规用途”和“内存优化”层中准备主服务器。

主服务器上的 `azure.replication_support` 参数必须设置为 **REPLICA**。 更改此静态参数时，需要重新启动服务器才能使更改生效。

1. 将 `azure.replication_support` 设置为副本。

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [重新启动服务器](/rest/api/postgresql/servers/restart)以应用更改。

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>创建只读副本
可以使用[CREATE API](/rest/api/postgresql/servers/create)创建读取副本：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> 若要了解有关可以在中创建副本的区域的详细信息，请访问[读取副本概念一文](concepts-read-replicas.md)。 

如果未在常规用途或内存优化的主服务器上将 `azure.replication_support` 参数设置为**副本**，然后重新启动服务器，则会收到错误。 在创建副本之前完成这两个步骤。

使用与 master 相同的计算和存储设置创建副本。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。


> [!IMPORTANT]
> 将主服务器设置更新为新值之前，请将副本设置更新为一个相等或更大的值。 此操作可帮助副本保持对主副本所做的任何更改。

### <a name="list-replicas"></a>列出副本
您可以使用[副本列表 API](/rest/api/postgresql/replicas/listbyserver)查看主服务器的副本列表：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
您可以使用[更新 API](/rest/api/postgresql/servers/update)来停止主服务器和读取副本之间的复制。

停止复制到主服务器和只读副本后，无法撤消该操作。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>删除主服务器或副本服务器
若要删除主服务器或副本服务器，请使用[DELETE API](/rest/api/postgresql/servers/delete)：

删除主服务器后，将停止复制到所有只读副本。 只读副本将成为支持读取和写入的独立服务器。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure Database for PostgreSQL 中的只读副本](concepts-read-replicas.md)。
* 了解如何[在 Azure 门户中创建和管理只读副本](howto-read-replicas-portal.md)。