---
title: 在高级计划中创建函数应用 - Azure CLI
description: 使用 Azure CLI 在 Azure 中的可缩放高级计划中创建函数应用
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.openlocfilehash: dd31dbadce4f0a55853607504b4322277784f27f
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534584"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>在高级计划中创建函数应用 - Azure CLI

此 Azure Functions 示例脚本将创建一个函数应用，作为函数的容器。 所创建的函数应用使用[可缩放的高级计划](../functions-premium-plan.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

此脚本会创建一个使用[高级计划](../functions-premium-plan.md)的函数应用。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建 Azure 存储帐户。 |
| [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | 创建高级计划。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 在应用服务计划中创建函数应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。
