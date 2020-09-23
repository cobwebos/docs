---
title: 管理服务器 Azure CLI-Azure Database for MySQL 灵活服务器
description: 了解如何从 Azure CLI 管理 Azure Database for MySQL 灵活的服务器。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 3798396c72bc01bc20f1b4ee3ee66961fe33bff5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933396"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure Database for MySQL 灵活的服务器 (预览版) 

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍如何管理在 Azure 中部署的灵活服务器 (预览版) 。 管理任务包括计算和存储缩放、管理员密码重置，以及查看服务器详细信息。

## <a name="prerequisites"></a>先决条件
如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。 本文要求在本地运行 Azure CLI 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

你将需要使用 [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) 命令登录到你的帐户。 请注意 id 属性，该属性指的是 Azure 帐户的订阅 ID。

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令选择帐户下的特定订阅。 记下 az login 输出中的 id 值，以用作命令中订阅参数的值。 如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> 如果尚未创建灵活的服务器，请创建一个，以便开始使用此操作方法指南。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

可以使用以下命令轻松增加计算层、Vcore 和存储空间。 你可以查看所有服务器操作，你可以执行 [az mysql 挠性 server server 概述](/cli/azure/mysql/server)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

下面是上述参数的详细信息：

**设置** | **示例值** | **说明**
---|---|---
name | mydemoserver | 输入 Azure Database for MySQL 服务器的唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。
resource-group | myresourcegroup | 提供 Azure 资源组的名称。
sku-name|Standard_D4ds_v4|输入计算层和大小的名称。 遵循以下约定 Standard_ {VM size} （简写形式）。 有关详细信息，请参阅[定价层](../concepts-pricing-tiers.md)。
storage-size | 6144 | 服务器的存储容量（以 MB 为单位）。 最小5120，增加1024增量。

> [!Important]
> - 存储可以向上扩展 (但是，你不能缩小存储) 


## <a name="manage-mysql-databases-on-a-server"></a>在服务器上管理 MySQL 数据库。
您可以使用这些命令中的任何一种来创建、删除、列出和查看服务器上数据库的数据库属性

| Cmdlet | 使用情况| 说明 |
| --- | ---| --- |
|[az mysql 挠性-server db create](/cli/azure/sql/db#az-mysql-flexible-server-db-create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |创建数据库|
|[az mysql 挠性-server db 删除](/cli/azure/sql/db#az-mysql-flexible-server-db-delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|从服务器中删除数据库。 此命令不会删除你的服务器。 |
|[az mysql 挠性-server db 列表](/cli/azure/sql/db#az-mysql-flexible-server-db-list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|列出服务器上的所有数据库|
|[az mysql 挠性-server db show](/cli/azure/sql/db#az-mysql-flexible-server-db-show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|显示数据库的更多详细信息|

## <a name="update-admin-password"></a>更新管理员密码
可以通过以下命令更改管理员角色的密码
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  请确保密码最少8个字符，最多128个字符。
> 密码必须包含以下类别中的三类：英文大写字母、英文小写字母、数字和非字母数字字符。

## <a name="delete-a-server"></a>删除服务器
如果要删除 MySQL 灵活服务器，可以运行 [az MySQL 挠性 server server delete](/cli/azure/mysql/server#az-mysql-flexible-server-delete) 命令。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤
- [了解如何启动或停止服务器](how-to-stop-start-server-portal.md)
- [了解如何管理虚拟网络](how-to-manage-virtual-network-cli.md)
- [排查连接问题](how-to-troubleshoot-common-connection-issues.md)
- [创建和管理防火墙](how-to-manage-firewall-cli.md)
