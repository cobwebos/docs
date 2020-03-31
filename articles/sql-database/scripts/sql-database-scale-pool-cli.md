---
title: CLI 示例 - 缩放 SQL 弹性池 - Azure SQL 数据库
description: 在 Azure SQL 数据库中缩放弹性池的 Azure CLI 示例脚本
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: b229aa8976705c5e3ad83c468ebc10a261f14a4f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067391"
---
# <a name="use-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>使用 CLI 在 Azure SQL 数据库中缩放弹性池

此 Azure CLI 脚本示例创建弹性池、移动共用数据库，并更改弹性池计算大小。

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

### <a name="sign-in-to-azure"></a>登录 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>运行脚本

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>示例参考

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | 服务器命令。 |
| [az sql db](/cli/azure/sql/db) | 数据库命令。 |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | 弹性池命令。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../sql-database-cli-samples.md)中找到。
