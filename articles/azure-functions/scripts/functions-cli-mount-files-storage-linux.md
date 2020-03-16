---
title: 将文件共享装载到 Python 函数应用 - Azure CLI
description: 使用 Azure CLI 创建无服务器 Python 函数应用并装载现有的文件共享。
ms.topic: sample
ms.date: 03/01/2020
ms.openlocfilehash: 3d66f84d124b36f1be335c3a2204f21690510ee8
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086284"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>使用 Azure CLI 将文件共享装载到 Python 函数应用

此 Azure Functions 示例脚本会创建一个函数应用，并会在 Azure 文件存储中创建一个共享。 然后，它会装载共享，使函数可以访问数据。  

>[!NOTE]
>创建的函数应用在 Python 版本 3.7 上运行。 Azure Functions 也[支持 Python 版本 3.6 和 3.8](../functions-reference-python.md#python-version)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 示例是为 Bash shell 编写的，必须将其修改为在 Windows 命令提示符下运行。 

## <a name="sample-script"></a>示例脚本

此脚本使用[消耗计划](../functions-scale.md#consumption-plan)创建 Azure 函数应用。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建 Azure 存储帐户。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 创建 Function App。 |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | 在存储帐户中创建 Azure 文件存储共享。 | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | 在共享中创建目录。 |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | 将共享装载到函数应用。 |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | 显示装载到函数应用的文件共享。 | 

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。
