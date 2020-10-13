---
title: 管理只读副本 - Azure CLI、REST API - Azure Database for MySQL
description: 了解如何使用 Azure CLI 或 REST API 在 Azure Database for MySQL 中设置和管理只读副本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 74e62c39295d36132abdce0abc033162fa22cb64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531626"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>如何使用 Azure CLI 和 REST API 在 Azure Database for MySQL 中创建和管理只读副本

本文介绍如何使用 Azure CLI 和 REST API 在 Azure Database for MySQL 服务中创建和管理只读副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

## <a name="azure-cli"></a>Azure CLI
可以使用 Azure CLI 创建和管理只读副本。

### <a name="prerequisites"></a>先决条件

- [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 将用作源服务器的 [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md) 。 

> [!IMPORTANT]
> 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 服务器。 请确保源服务器位于其中一个定价层中。

### <a name="create-a-read-replica"></a>创建只读副本

> [!IMPORTANT]
> 为没有现有副本的源创建副本时，会先重新启动源以准备复制的副本。 请考虑这一点并在非高峰期执行这些操作。

可以使用以下命令创建只读副本服务器：

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  要在其中创建副本服务器的资源组。  |
| name | mydemoreplicaserver | 所创建的新副本服务器的名称。 |
| source-server | mydemoserver | 要从中进行复制的现有源服务器的名称或 ID。 |

若要创建跨区域只读副本，请使用 `--location` 参数。 下面的 CLI 示例在美国西部创建副本。

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

> [!NOTE]
> 只读副本使用与主服务器相同的服务器配置创建。 副本服务器配置在创建后可以更改。 建议副本服务器的配置应保留为等于或大于源的值，以确保副本能够与主副本保持同步。


### <a name="list-replicas-for-a-source-server"></a>列出源服务器的副本

若要查看给定源服务器的所有副本，请运行以下命令： 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  要在其中创建副本服务器的资源组。  |
| server-name | mydemoserver | 源服务器的名称或 ID。 |

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器

> [!IMPORTANT]
> 停止复制到服务器操作不可逆。 源和副本之间的复制停止后，无法撤消。 然后，副本服务器将成为独立服务器，并且现在支持读取和写入。 此服务器不能再次成为副本服务器。

可以使用以下命令停止复制到只读副本服务器：

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  副本服务器所在的资源组。  |
| name | mydemoreplicaserver | 要停止在其上进行复制的副本服务器的名称。 |

### <a name="delete-a-replica-server"></a>删除副本服务器

可以通过运行 **[az mysql server delete](/cli/azure/mysql/server)** 命令删除只读副本服务器。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>删除源服务器

> [!IMPORTANT]
> 删除源服务器会停止复制到所有副本服务器，并删除源服务器本身。 副本服务器成为现在支持读取和写入的独立服务器。

若要删除源服务器，可运行 **[az mysql server delete](/cli/azure/mysql/server)** 命令。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
可以使用 [Azure REST API](/rest/api/azure/) 创建和管理只读副本。

### <a name="create-a-read-replica"></a>创建只读副本
可以使用[创建 API](/rest/api/mysql/servers/create) 创建只读副本：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

如果未将参数设置 `azure.replication_support` 为常规用途或内存优化源服务器上的 **副本** ，并重新启动服务器，则会收到错误。 请在创建副本之前完成这两个步骤。

使用与主服务器相同的计算和存储设置创建副本。 创建副本后，可以独立于源服务器更改多个设置：计算生成、Vcore、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。


> [!IMPORTANT]
> 在将源服务器设置更新为新值之前，请将副本设置更新为一个相等或更大的值。 此操作可帮助副本与主服务器发生的任何更改保持同步。

### <a name="list-replicas"></a>列出副本
您可以使用 [副本列表 API](/rest/api/mysql/replicas/listbyserver)查看源服务器的副本列表：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
您可以使用 [更新 API](/rest/api/mysql/servers/update)来停止源服务器和读取副本之间的复制。

停止复制到源服务器和读取副本后，无法撤消。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>删除源或副本服务器
若要删除源或副本服务器，请使用 [删除 API](/rest/api/mysql/servers/delete)：

删除源服务器时，将停止复制到所有读取副本。 只读副本将成为支持读取和写入的独立服务器。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>后续步骤

- 详细了解[只读副本](concepts-read-replicas.md)
