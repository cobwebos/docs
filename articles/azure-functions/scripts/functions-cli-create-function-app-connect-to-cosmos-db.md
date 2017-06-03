---
title: "创建用于连接到 Azure Cosmos DB 的 Azure Function | Microsoft Docs"
description: "Azure CLI 脚本示例 - 创建用于连接到 Azure Cosmos DB 的 Azure Function"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: dbe02fbc71f04c67ab03a84845bf8666333ce621
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>创建用于连接到 Azure Cosmos DB 的 Azure Function

此示例脚本创建 Azure Function App，并连接到 Azure Cosmos DB 数据库。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

此示例创建 Azure Function app，并将 Cosmos DB 终结点和访问密钥添加到应用设置。

[!code-azurecli-interactive[主要](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "创建用于连接到 Azure Cosmos DB 的 Azure Function")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及所有相关资源。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | 登录到 Azure。 |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 使用相关位置创建资源组 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | 创建存储帐户 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | 创建一个新的 Function App |
| [az documentdb create](https://docs.microsoft.com/cli/azure/documentdb#create) | 创建 documentdb 数据库 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | 清理 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。





