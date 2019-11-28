---
title: 在 Azure 中的 Linux 上创建第一个函数
description: 了解如何使用命令行工具、Azure Functions Core Tools 和 Azure CLI 创建第一个托管在 Azure 中的 Linux 上的函数。
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 19abfee69db53c560dfa2696d85f8c1c3d770c09
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230818"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>快速入门：使用命令行工具创建第一个在 Linux 上托管的函数

Azure Functions 用于在[无服务器](https://azure.com/serverless) Linux 环境中执行代码，无需先创建 VM 或发布 Web 应用程序。 Linux 托管需要 [Functions 2.x 运行时](functions-versions.md)。 无服务器函数在[消耗计划](functions-scale.md#consumption-plan)中运行。

本快速入门文章逐步讲解如何使用 Azure CLI 创建第一个在 Linux 上运行的函数应用。 函数代码在本地创建，然后使用 [Azure Functions Core Tools](functions-run-local.md) 部署到 Azure。

支持在 Mac、Windows 或 Linux 计算机上执行以下步骤。 本文展示了如何使用 JavaScript 或 C# 创建函数。 若要了解如何创建 Python 函数，请参阅[使用 Core Tools 和 Azure CLI 创建第一个 Python 函数](functions-create-first-function-python.md)。

## <a name="prerequisites"></a>先决条件

运行此示例之前，必须做好以下准备：

+ 安装 [Azure Functions Core Tools](./functions-run-local.md#v2) 版本 2.6.666 或更高版本。

+ 安装 [Azure CLI]( /cli/azure/install-azure-cli)。 本文需要 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 你也可使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

+ 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>启用扩展捆绑包

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

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