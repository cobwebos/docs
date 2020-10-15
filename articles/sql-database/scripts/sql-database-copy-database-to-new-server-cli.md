---
title: Azure CLI：将 Azure SQL 数据库中的数据库复制到新服务器
description: 用于将 Azure SQL 数据库中的数据库复制到新服务器的 Azure CLI 示例脚本
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: e22d28215c06de96d31faec71f4fb595bb74a4a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87495261"
---
# <a name="use-cli-to-copy-a-database-in-azure-sql-database-to-a-new-server"></a>使用 CLI 将 Azure SQL 数据库中的数据库复制到新服务器

以下 Azure CLI 脚本示例在新服务器中创建现有数据库的副本。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

### <a name="sign-in-to-azure"></a>登录 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>运行脚本

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.sh "Copy database to new server")]

### <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name $resource
az group delete --name $targetResource
```

## <a name="sample-reference"></a>示例参考

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az sql db copy](/cli/azure/sql/db#az-sql-db-copy) | 创建当前使用快照的数据库副本。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../../azure-sql/database/az-cli-script-samples-content-guide.md)中找到。
