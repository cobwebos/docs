---
title: "Azure CLI 脚本示例 - 创建可无服务器执行的 Function App | Microsoft Docs"
description: "Azure CLI 脚本示例 - 创建可无服务器执行的 Function App"
services: functions
documentationcenter: functions
author: syntaxc4
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 37046967bd5ab0d797d1c66690db7200fb4805e2
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# <a name="create-a-function-app-for-serverless-execution"></a>创建可无服务器执行的 Function App

此示例脚本将创建一个 Azure Function App，作为函数的容器。 将使用[消耗计划](../functions-scale.md#consumption-plan)创建最适合事件驱动无服务器工作负荷的 Function App。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

此脚本使用[消耗计划](../functions-scale.md#consumption-plan)创建 Azure Function app。

[!code-azurecli-interactive[主要](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "基于消耗计划创建 Azure Function")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#create) | 创建 Azure 存储帐户。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | 创建 Azure Function。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。

