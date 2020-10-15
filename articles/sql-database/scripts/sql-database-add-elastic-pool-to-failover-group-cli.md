---
title: CLI 示例 - 故障转移组 - Azure SQL 数据库弹性池
description: Azure CLI 示例脚本，用于创建 Azure SQL 数据库弹性池，将其添加到故障转移组，然后测试故障转移。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 07/16/2019
ms.openlocfilehash: a5814bfe3bd6ec2d97a068ea8ce71fa7ffea8ec0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323577"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>使用 CLI 将 Azure SQL 数据库弹性池添加到故障转移组

此 Azure CLI 脚本示例创建一个单一数据库，将其添加到弹性池，创建一个故障转移组，然后测试故障转移。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

### <a name="sign-in-to-azure"></a>登录 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>运行脚本

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>示例参考

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 描述 |
|---|---|
| [az sql elastic-pool](/cli/azure/sql/elastic-pool) | 弹性池命令。 |
| [az sql failover-group ](/cli/azure/sql/failover-group) | 故障转移组命令。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/overview)。

可以在 [Azure SQL 数据库 Azure CLI 脚本](../../azure-sql/database/az-cli-script-samples-content-guide.md)中找到其他 SQL 数据库 Azure CLI 脚本示例。
