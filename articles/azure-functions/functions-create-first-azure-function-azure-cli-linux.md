---
title: 在 Azure 中的 Linux 上创建第一个函数
description: 了解如何使用 Azure Functions Core Tools 和 Azure CLI 创建第一个托管在 Azure 中的 Linux 上的函数。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 09/12/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: multiple
manager: jeconnoc
ms.openlocfilehash: 1045e0cc0d114bb8b35e6136a2054b3642eac7e8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249858"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>使用 Core Tools 和 Azure CLI（预览版）创建第一个托管在 Linux 上的函数

Azure Functions 用于在[无服务器](https://azure.microsoft.com/overview/serverless-computing/) Linux 环境中执行代码，无需先创建 VM 或发布 Web 应用程序。 Linux 托管目前为预览版，并且需要 [Functions 2.0 运行时](functions-versions.md)。

本快速入门文章逐步讲解如何使用 Azure CLI 创建第一个在 Linux 上运行的函数应用。 函数代码在本地创建，然后使用 [Azure Functions Core Tools](functions-run-local.md) 部署到 Azure。

支持在 Mac、Windows 或 Linux 计算机上执行以下步骤。 本文展示了如何使用 JavaScript 或 C# 创建函数。

## <a name="prerequisites"></a>先决条件

运行此示例之前，必须做好以下准备：

+ 安装 [Azure Core Tools 2.x 版](functions-run-local.md#v2)。

+ 安装 [Azure CLI]( /cli/azure/install-azure-cli)。 本文需要 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 你也可使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

+ 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>创建本地函数应用项目

从命令行运行以下命令，以便在当前本地目录的 `MyFunctionProj` 文件夹中创建一个函数应用项目。 也会在 `MyFunctionProj` 中创建一个 GitHub 存储库。

```bash
func init MyFunctionProj
```

当系统提示时，请使用箭头键从下面的语言选项中选择一个辅助角色运行时：

+ `dotnet`：创建一个 .NET 类库项目 (.csproj)。
+ `node`：创建一个 JavaScript 项目。

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>在 Azure 中创建 Linux 函数应用

必须使用函数应用在 Linux 上托管函数的执行。 此函数应用提供一个无服务器环境，用于执行函数代码。 它可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 使用 [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令创建在 Linux 上运行的函数应用。

在以下命令中，请在 `<app_name>` 占位符处使用唯一的函数应用名称，在 `<storage_name>` 处使用存储帐户名称。 `<app_name>` 也是函数应用的默认 DNS 域。 此名称在 Azure 的所有应用中必须独一无二。

```azurecli
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--location "westus" --is-linux
```

> [!NOTE]
> 如果现有资源组名为 `myResourceGroup` 且其中包含非 Linux 应用服务应用，则必须使用另一资源组。 不能在同一资源组中托管 Windows 和 Linux 应用。  

创建函数应用后，会看到以下消息：

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

现在，可以将项目发布到 Azure 中的新函数应用。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

本文介绍了如何在默认 Azure 应用服务容器中托管函数应用。 也可将函数托管在 Linux 上你自己的自定义容器中。

> [!div class="nextstepaction"] 
> [在 Linux 上使用自定义映像创建函数](functions-create-function-linux-custom-image.md)
