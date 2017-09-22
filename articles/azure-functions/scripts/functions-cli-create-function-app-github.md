---
title: "创建 Function App 并从 GitHub 部署函数代码 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 创建 Function App 并从 GitHub 部署函数代码"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 39e26ba6c3ae0927fbf6c62af0bd3b48d16657ad
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>创建 Function App 并从 GitHub 部署函数代码

此示例脚本使用[消耗计划](../functions-scale.md#consumption-plan)及其相关资源创建 Function App，并从公共 GitHub 存储库部署函数代码（不进行持续部署）。 有关从 GitHub 持续交付函数代码，请阅读[创建 Function App 并从 GitHub 进行持续部署](functions-cli-create-function-app-github-continuous.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

此示例创建 Azure Function app 并从 GitHub 部署函数代码。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "从 GitHub 使用部署创建 Function App")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) | 创建 Azure Function App。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | 将 Function App 与 Git 或 Mercurial 存储库相关联。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。

