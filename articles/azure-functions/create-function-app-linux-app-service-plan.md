---
title: 在 Azure 应用服务计划中创建 Linux 上的函数应用
description: 了解如何使用 Azure CLI 在应用服务计划中创建在 Linux 上运行的函数应用。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52854124"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>在 Azure 应用服务计划中创建 Linux 函数应用（预览）

使用 Azure Functions 可将函数托管在 Linux 上的默认 Azure 应用服务容器中。 本文逐步讲解了如何使用 Azure CLI 在 Azure 中创建运行于[应用服务计划](functions-scale.md#app-service-plan)中的 Linux 托管的函数应用。 还可以[自带自定义的容器](functions-create-function-linux-custom-image.md)。 Linux 托管目前以预览版提供。

在应用服务计划中，你负责函数应用的缩放。 也可将 Linux 上的函数托管在[消耗计划](functions-scale.md#consumption-plan)中，以便利用 Azure Functions 的无服务器功能。

可以使用 Mac、Windows 或 Linux 计算机执行以下步骤。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

+ 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求使用 Azure CLI 2.0.21 版或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>创建 Linux 应用服务计划

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>在 Linux 上创建函数应用

必须使用函数应用在 Linux 上托管函数的执行。 函数应用提供一个用于执行函数代码的环境。 它可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令在 Linux 应用服务计划中创建一个函数应用。

在以下命令中，请将 `<app_name>` 占位符替换成唯一函数应用名称，将 `<storage_name>` 替换为存储帐户名。 `<app_name>` 将用作 Function App 的默认 DNS 域，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 你还应该通过 `dotnet` (C#)、`node` (JavaScript) 或 `python` 为函数应用设置 `<language>` 运行时。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

创建并部署函数应用后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

由于 `myAppServicePlan` 是 Linux 计划，因此使用了内置的 Docker 映像来创建在 Linux 上运行函数应用的容器。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>后续步骤

本文介绍如何在 Azure 中创建 Linux 托管的函数应用。 现在可以将[函数项目部署](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest)到此函数应用。 可以使用 Azure Functions Core Tools 在本地计算机上[创建函数项目](functions-run-local.md#create-a-local-functions-project)，然后将它部署到新的 Linux 函数应用。  

> [!div class="nextstepaction"] 
> [在本地对 Azure Functions 进行编码和测试](functions-run-local.md)
