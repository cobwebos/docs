---
title: 管理适用于 Azure CLI 从 PostgreSQL 的 Azure 数据库的只读的副本
description: 了解如何管理 Azure Database for PostgreSQL 读取副本从 Azure CLI。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/29/2019
ms.openlocfilehash: 4c8502aad40662766b038205eb19dd3302f601b7
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631996"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>创建和管理从 Azure CLI 的只读的副本

在本文中，您将学习如何创建和管理用于从 Azure CLI 的 PostgreSQL 的 Azure 数据库中的只读的副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

> [!IMPORTANT]
> 只读副本功能目前以公共预览版提供。

## <a name="prerequisites"></a>必备组件
- 用作主服务器的 [Azure Database for PostgreSQL 服务器](quickstart-create-server-up-azure-cli.md)。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 


## <a name="prepare-the-master-server"></a>准备主服务器
必须使用以下步骤在“常规用途”和“内存优化”层中准备主服务器。

主服务器上的 `azure.replication_support` 参数必须设置为 **REPLICA**。 更改此静态参数后，重新启动服务器是必需的更改才能生效。

1. 设置`azure.replication_support`到副本。

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. 重新启动以将更改应用到服务器。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>创建只读副本

`az mysql server replica create` 命令需要以下参数：

| 设置 | 示例值 | 描述  |
| --- | --- | --- |
| resource-group | myresourcegroup |  资源组将在其中创建副本服务器。  |
| 名称 | mydemoserver-replica | 所创建的新副本服务器的名称。 |
| source-server | mydemoserver | 名称或资源 ID 的现有的主服务器将从复制。 |

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

如果尚未设置`azure.replication_support`参数**副本**在常规用途或内存优化的主服务器和重新启动服务器，收到错误。 完成这两个步骤后再创建一个副本。

副本是使用与主服务器相同的服务器配置创建的。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。

> [!IMPORTANT]
> 将主服务器的配置更新为新值之前，请将副本配置更新为与这些新值相等或更大的值。 此操作可确保副本与主服务器发生的任何更改保持同步。

## <a name="list-replicas"></a>列表副本
您可以查看副本的主服务器的列表。

```azurecli-interactive
az postgres server replica stop --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
可以停止主服务器与只读副本之间的复制。

停止复制到主服务器和只读副本后，无法撤消该操作。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>删除主服务器或副本服务器
若要删除的 master 或副本服务器，您可以使用相同的命令并删除独立的 Azure Database for PostgreSQL 服务器。 

删除主服务器后，将停止复制到所有只读副本。 只读副本将成为支持读取和写入的独立服务器。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤
详细了解 [Azure Database for PostgreSQL 中的只读副本](concepts-read-replicas.md)。