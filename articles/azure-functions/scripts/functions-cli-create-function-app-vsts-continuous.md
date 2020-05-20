---
title: 使用 DevOps 开发创建函数应用 - Azure CLI
description: 创建 Function App 并从 Azure DevOps 部署函数代码
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: d10c3987aef6e0a32081acd17425517f5109c4bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922645"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>在 Azure 中创建从 Azure DevOps 部署的函数

本主题演示如何使用 Azure Functions 创建使用[消耗计划](../functions-scale.md#consumption-plan)的[无服务器](https://azure.microsoft.com/solutions/serverless/)函数应用。 从 Azure DevOps 存储库连续部署函数应用（即函数的容器）。 

若要完成本主题，必须具备以下条件：

* 包含函数应用项目的 Azure DevOps 存储库，并且你对其具有管理权限。
* [个人访问令牌 (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)，用于访问 Azure DevOps 存储库。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果想要在本地使用 Azure CLI，必须安装和使用版本 2.0 或更高版本。 若要确定 Azure CLI 版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

此示例创建 Azure 函数应用并从 Azure DevOps 部署函数代码。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、存储帐户、函数应用和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建函数应用所需的存储帐户。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 在无服务器[消耗计划](../functions-scale.md#consumption-plan)中创建函数应用。 |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | 将 Function App 与 Git 或 Mercurial 存储库相关联。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。
