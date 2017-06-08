---
title: "Azure CLI 脚本 - 移动 SQL 数据库和弹性池 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 使用 Azure CLI 在弹性池之间移动 SQL 数据库"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & manage
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/24/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: df4a62783cf3f8f644de850cb80bdad8352cf372
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---

# <a name="create-elastic-pools-and-move-databases-between-pools-and-out-of-a-pool-using-the-azure-cli"></a>使用 Azure CLI 创建弹性池，并在池之间和池外移动数据库

此示例 CLI 脚本创建两个弹性池，将数据库从一个弹性池移到另一个弹性池中，然后将数据库移出弹性池，移到单一数据库性能级别。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[主要](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "在池之间移动数据库")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | 创建用于托管数据库或弹性池的逻辑服务器。 |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | 在逻辑服务器中创建弹性池。 |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | 在逻辑服务器中创建数据库作为单一数据库或入池数据库。 |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | 更新数据库属性，或者将数据库移入、移出弹性池或在弹性池之间移动。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../sql-database-cli-samples.md)中找到。



