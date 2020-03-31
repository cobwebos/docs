---
title: 将 BACPAC 文件导入 Azure SQL 数据库的 CLI 示例
description: 用于将 BACPAC 文件导入 SQL 数据库的 Azure CLI 示例脚本
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: load & move data
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 05/24/2019
ms.openlocfilehash: f47d0ec2edf54aaa708c176e92a9e1026b446882
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061838"
---
# <a name="use-cli-to-import-a-bacpac-file-into-an-azure-sql-database"></a>使用 CLI 将 BACPAC 文件导入 Azure SQL 数据库

以下 Azure CLI 脚本示例将数据库从 *.bacpac* 文件导入 Azure SQL 数据库。  

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

### <a name="sign-in-to-azure"></a>登录 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>运行脚本

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/import-from-bacpac/import-from-bacpac.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>示例参考

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | 服务器命令。 |
| [az sql db import](/cli/azure/sql/db#az-sql-db-import) | 数据库导入命令。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../sql-database-cli-samples.md)中找到。
