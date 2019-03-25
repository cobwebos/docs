---
title: 在 Azure 中的 Linux 上创建第一个函数
description: 了解如何使用 Azure Functions Core Tools 和 Azure CLI 创建第一个托管在 Azure 中的 Linux 上的函数。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 03/12/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc, fasttrack-edit
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: 78c2f599ba7d22e6de070f5867398e111a396d45
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849731"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>使用 Core Tools 和 Azure CLI（预览版）创建第一个托管在 Linux 上的函数

Azure Functions 用于在[无服务器](https://azure.com/serverless) Linux 环境中执行代码，无需先创建 VM 或发布 Web 应用程序。 Linux 托管需要 [Functions 2.0 运行时](functions-versions.md)。 在无服务器的[消耗计划](functions-scale.md#consumption-plan)中支持在 Linux 上运行函数应用，目前处于预览状态。 若要了解详细信息，请参阅[此预览注意事项文章](https://aka.ms/funclinux)。

本快速入门文章逐步讲解如何使用 Azure CLI 创建第一个在 Linux 上运行的函数应用。 函数代码在本地创建，然后使用 [Azure Functions Core Tools](functions-run-local.md) 部署到 Azure。

支持在 Mac、Windows 或 Linux 计算机上执行以下步骤。 本文展示了如何使用 JavaScript 或 C# 创建函数。 若要了解如何创建 Python 函数，请参阅[使用 Core Tools 和 Azure CLI（预览版）创建第一个 Python 函数](functions-create-first-function-python.md)。

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
+ `node`：创建一个 JavaScript 或 TypeScript 项目。 出现提示时，选择“`JavaScript`”。
+ `python`：创建 Python 项目。 有关 Python 函数，请参阅 [Python 快速入门](functions-create-first-function-python.md)。

执行命令后，看到的内容如以下输出所示：

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

使用以下命令导航到新的 `MyFunctionProj` 项目文件夹。

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>在 Azure 中创建 Linux 函数应用

必须使用函数应用在 Linux 上托管函数的执行。 此函数应用提供一个无服务器环境，用于执行函数代码。 它可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令创建在 Linux 上运行的函数应用。

在以下命令中，请在 `<app_name>` 占位符处使用唯一的函数应用名称，在 `<storage_name>` 处使用存储帐户名称。 `<app_name>` 也是函数应用的默认 DNS 域。 此名称在 Azure 的所有应用中必须独一无二。 还应该通过 `dotnet` (C#)、`node` (JavaScript/TypeScript) 或 `python` 为函数应用设置 `<language>` 运行时。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

创建函数应用后，会看到以下消息：

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

现在，可以将项目发布到 Azure 中的新函数应用。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]