---
title: 管理只读副本 - Azure CLI、REST API - Azure Database for PostgreSQL（单一服务器）
description: 了解如何通过 Azure CLI 和 REST API 管理 Azure Database for PostgreSQL（单一服务器）中的只读副本
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: e9be14548704557b4bdd39119294671852040348
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636574"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>通过 Azure CLI、REST API 创建和管理只读副本

本文介绍如何使用 Azure CLI 和 REST API 在 Azure Database for PostgreSQL 中创建和管理只读副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

## <a name="azure-replication-support"></a>Azure 复制支持
[读取副本](concepts-read-replicas.md)和[逻辑解码](concepts-logical.md)都依赖于 "Postgres 写入" 日志（WAL）来获取详细信息。 这两个功能需要来自 Postgres 的不同级别的日志记录。 逻辑解码需要比读取副本更高的日志记录级别。

若要配置正确的日志记录级别，请使用 Azure 复制支持参数。 Azure 复制支持有三个设置选项：

* **Off** -将最少的信息放在 WAL 中。 在大多数 Azure Database for PostgreSQL 服务器上，此设置不可用。  
* **副本**-比**Off**更详细。 这是[读取副本](concepts-read-replicas.md)运行所需的最小日志记录级别。 在大多数服务器上，此设置是默认设置。
* **逻辑**上比**副本**更详细。 这是要运行的逻辑解码的最小日志记录级别。 读取副本也可以通过此设置运行。

需要在更改此参数后重新启动服务器。 在内部，此参数设置 Postgres 参数 `wal_level` 、 `max_replication_slots` 和 `max_wal_senders` 。

## <a name="azure-cli"></a>Azure CLI
可以使用 Azure CLI 创建和管理只读副本。

### <a name="prerequisites"></a>先决条件

- [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 用作主服务器的 [Azure Database for PostgreSQL 服务器](quickstart-create-server-up-azure-cli.md)。


### <a name="prepare-the-master-server"></a>准备主服务器

1. 检查主服务器的 `azure.replication_support` 值。 它至少应该是副本，读取副本才能工作。

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. 如果不 `azure.replication_support` 是至少副本，则设置它。 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. 重启服务器以应用更改。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>创建只读副本

[az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| resource-group | myresourcegroup |  要在其中创建副本服务器的资源组。  |
| name | mydemoserver-replica | 所创建的新副本服务器的名称。 |
| source-server | mydemoserver | 要从中进行复制的现有主服务器的名称或资源 ID。 如果希望副本和主资源组不同，请使用资源 ID。 |

在下面的 CLI 示例中，副本在主服务器所在的区域中创建。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

若要创建跨区域只读副本，请使用 `--location` 参数。 下面的 CLI 示例在美国西部创建副本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

如果尚未在“常规用途”或“内存优化”主服务器上将 `azure.replication_support` 参数设置为 **REPLICA** 并重启服务器，将会收到错误。 请在创建副本之前完成这两个步骤。

使用与主服务器相同的计算和存储设置创建副本。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。

> [!IMPORTANT]
> 将主服务器设置更新为新值之前，请将副本设置更新为一个相等或更大的值。 此操作可帮助副本与主服务器发生的任何更改保持同步。

### <a name="list-replicas"></a>列出副本
可以使用 [az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) 命令查看主服务器的副本的列表。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
可以使用 [az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) 命令停止在主服务器和只读副本之间的复制。

停止复制到主服务器和只读副本后，无法撤消该操作。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>删除主服务器或副本服务器
若要删除主服务器或副本服务器，请使用 [az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) 命令。

删除主服务器后，将停止复制到所有只读副本。 只读副本将成为支持读取和写入的独立服务器。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
可以使用 [Azure REST API](/rest/api/azure/) 创建和管理只读副本。

### <a name="prepare-the-master-server"></a>准备主服务器

1. 检查主服务器的 `azure.replication_support` 值。 它至少应该是副本，读取副本才能工作。

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. 如果不 `azure.replication_support` 是至少副本，则设置它。

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
可以使用[创建 API](/rest/api/postgresql/servers/create) 创建只读副本：

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
> 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

如果尚未在“常规用途”或“内存优化”主服务器上将 `azure.replication_support` 参数设置为 **REPLICA** 并重启服务器，将会收到错误。 请在创建副本之前完成这两个步骤。

使用与主服务器相同的计算和存储设置创建副本。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。


> [!IMPORTANT]
> 将主服务器设置更新为新值之前，请将副本设置更新为一个相等或更大的值。 此操作可帮助副本与主服务器发生的任何更改保持同步。

### <a name="list-replicas"></a>列出副本
可以使用[副本列表 API](/rest/api/postgresql/replicas/listbyserver) 查看主服务器的副本列表：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
可以使用[更新 API](/rest/api/postgresql/servers/update) 停止主服务器与只读副本之间的复制。

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
若要删除主服务器或副本服务器，请使用[删除 API](/rest/api/postgresql/servers/delete)：

删除主服务器后，将停止复制到所有只读副本。 只读副本将成为支持读取和写入的独立服务器。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure Database for PostgreSQL 中的只读副本](concepts-read-replicas.md)。
* 了解如何[在 Azure 门户中创建和管理只读副本](howto-read-replicas-portal.md)。
