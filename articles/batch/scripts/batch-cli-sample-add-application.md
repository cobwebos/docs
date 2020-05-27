---
title: Azure CLI 脚本示例 - 在 Batch 中添加应用程序
description: 此示例脚本演示如何添加要与 Azure Batch 池或任务配合使用的应用程序。
ms.topic: sample
ms.date: 01/29/2018
ms.openlocfilehash: 792af55be17551d1635b6821456c6e8e53e9daea
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681934"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI 示例：向 Azure Batch 帐户添加应用程序

此脚本演示如何添加要与 Azure Batch 池或任务配合使用的应用程序。 若要设置添加到 Batch 帐户的应用程序，请将可执行文件与所有依赖文件一起打包为 zip 文件。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.20 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。
表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建存储帐户。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | 创建批处理帐户。 |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | 创建应用程序。  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | 将应用程序包添加到指定的应用程序。  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | 更新应用程序的属性。  |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。
