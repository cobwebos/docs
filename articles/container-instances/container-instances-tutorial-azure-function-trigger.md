---
title: 教程 - 通过 Azure 函数触发容器组
description: 创建 HTTP 触发的无服务器 PowerShell 函数，以便自动创建 Azure 容器实例
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78331019"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>教程：使用 HTTP 触发的 Azure 函数创建容器组

[Azure Functions](../azure-functions/functions-overview.md) 是一项无服务器计算服务，它可以运行脚本或代码来响应各种事件，例如 HTTP 请求、计时器或 Azure 存储队列中的消息。

在本教程中，我们将创建一个 Azure 函数，该函数接受 HTTP 请求并触发对[容器组](container-instances-container-groups.md)的部署。 此示例演示通过 Azure Functions 在 Azure 容器实例中自动创建资源的基本步骤。 对于更复杂的方案或其他事件触发器，请修改或扩展此示例。 

学习如何：

> [!div class="checklist"]
> * 将 Visual Studio Code 与 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)配合使用，创建基本的 HTTP 触发的 PowerShell 函数。
> * 在函数应用中启用一个标识，并为其授予创建 Azure 资源的权限。
> * 修改并重新发布用于自动部署单容器容器组的 PowerShell 函数。
> * 验证对容器进行的 HTTP 触发的部署。

> [!IMPORTANT]
> 适用于 Azure Functions 的 PowerShell 当前为预览版。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="prerequisites"></a>先决条件

请参阅[在 Azure 中创建第一个函数](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment)，了解在 OS 上安装 Visual Studio Code 并将其与 Azure Functions 配合使用的先决条件。

本文中的一些步骤使用 Azure CLI。 可以使用 Azure Cloud Shell 或本地安装的 Azure CLI 完成这些步骤。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-a-basic-powershell-function"></a>创建基本的 PowerShell 函数

请按[在 Azure 中创建第一个 PowerShell 函数](../azure-functions/functions-create-first-function-powershell.md)中的步骤操作，使用“HTTP 触发器”模板创建 PowerShell 函数。 使用默认的 Azure 函数名称 **HttpTrigger**。 按快速入门中的演示操作，在本地测试函数，并将项目发布到 Azure 中的函数应用。 此示例是一个基本的 HTTP 触发的函数，返回文本字符串。 在本文后面的步骤中，我们通过修改该函数来创建容器组。

本文假定你在一个 Azure 资源组中使用名称 *myfunctionapp* 发布项目，该资源组自动根据函数应用名称（也是 *myfunctionapp*）命名。 请在后面的步骤中将上述名称替换为自己的唯一函数应用名称和资源组名称。

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>在函数应用中启用 Azure 托管标识

现在，在函数应用中启用系统分配的[托管标识](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity)。 运行此应用的 PowerShell 主机可以使用此标识自动进行身份验证，使函数能够在标识有权访问的 Azure 服务上执行操作。 在本教程中，我们授予托管标识在函数应用的资源组中创建资源的权限。 

首先使用 [az group show][az-group-show] 命令获取函数应用的资源组的 ID 并将其存储在环境变量中。 以下示例假定你在 Bash shell 中运行该命令。

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

运行 [az functionapp identity app assign][az-functionapp-identity-app-assign]，为函数应用分配本地标识，为资源组分配参与者角色。 此角色允许标识在资源组中创建其他资源，例如容器组。

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>修改 HttpTrigger 函数

可以通过修改 **HttpTrigger** 函数的 PowerShell 代码来创建容器组。 在函数的 `run.ps1` 文件中，找到以下代码块。 此代码显示名称值（如果在函数 URL 中以查询字符串的形式传递了一个名称值）：

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

请将该代码替换为以下示例块。 在这里，如果在查询字符串中传递了某个名称值，则会通过 [New-AzContainerGroup][new-azcontainergroup] cmdlet 使用该值来命名并创建容器组。 请确保将资源组名称 *myfunctionapp* 替换为函数应用的资源组的名称：

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

此示例创建一个容器组，该容器组包含的单个容器实例运行 `alpine` 映像。 该容器运行单个 `echo` 命令，然后终止。 在实际示例中，我们可能会触发创建操作，创建一个或多个用于运行批处理作业的容器组。
 
## <a name="test-function-app-locally"></a>在本地测试函数应用

请确保函数在本地正常运行，然后再将函数应用项目重新发布到 Azure。 如 [PowerShell 快速入门](../azure-functions/functions-create-first-function-powershell.md)所示，请在 PowerShell 脚本中插入一个本地断点，然后在其上插入 `Wait-Debugger` 调用。 有关调试指南，请参阅[在本地调试 PowerShell Azure Functions](../azure-functions/functions-debug-powershell-local.md)。


## <a name="republish-azure-function-app"></a>重新发布 Azure 函数应用

验证该函数可以在本地计算机上正确运行以后，即可将项目重新发布到 Azure 中的现有函数应用。

> [!NOTE]
> 将函数发布到 Azure 之前，请记得删除对 `Wait-Debugger` 的所有调用。

1. 在 Visual Studio Code 中打开命令面板。 搜索并选择 `Azure Functions: Deploy to function app...`。
1. 选择当前的用于压缩和部署的工作文件夹。
1. 选择订阅，然后选择现有函数应用的名称 (*myfunctionapp*)。 确认要覆盖以前的部署。

创建函数应用并应用了部署包之后，会显示一个通知。 在此通知中选择“查看输出”  以查看创建和部署结果，其中包括已更新的 Azure 资源。

## <a name="run-the-function-in-azure"></a>在 Azure 中运行函数

部署成功完成以后，请获取函数 URL。 例如，使用 Visual Studio Code 中的“Azure:  Functions”区域复制 **HttpTrigger** 函数 URL，或者在 [Azure 门户](../azure-functions/functions-create-first-azure-function.md#test-the-function)中获取函数 URL。

函数 URL 包含唯一代码，采用以下形式：

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>在不传递名称的情况下运行函数

首次测试时，请运行 `curl` 命令，传递没有追加 `name` 查询字符串的函数 URL。 请确保包括函数的唯一代码。

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

此函数返回状态代码 400 和文本“`Please pass a name on the query string or in the request body`”：

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>运行函数并传递容器组的名称

现在请运行 `curl` 命令，方法是：将容器组的名称 (*mycontainergroup*) 以查询字符串 `&name=mycontainergroup` 的形式追加：

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

此函数返回状态代码 200 并触发一项创建容器组的操作：

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

使用 [az container logs][az-container-logs] 命令验证容器是否已运行：

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

示例输出：

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要在本教程中创建的任何资源，可以执行 [az group delete][az-group-delete] 命令，删除资源组和其中包含的所有资源。 此命令将删除创建的容器注册表、正在运行的容器和所有相关资源。

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>后续步骤

在本教程中，我们创建了一个 Azure 函数，该函数接受 HTTP 请求并触发对容器组的部署。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 将 Visual Studio Code 与 Azure Functions 扩展配合使用，创建基本的 HTTP 触发的 PowerShell 函数。
> * 在函数应用中启用一个标识，并为其授予创建 Azure 资源的权限。
> * 修改用于自动部署单容器容器组的 PowerShell 函数代码。
> * 验证对容器进行的 HTTP 触发的部署。

有关如何启动并监视容器化作业的详细示例，请参阅博客文章 [Event-Driven Serverless Containers with PowerShell Azure Functions and Azure Container Instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b)（将事件驱动型无服务器容器与 PowerShell Azure Functions 和 Azure 容器实例配合使用）和随附的[代码示例](https://github.com/anthonychu/functions-powershell-run-aci)。

请参阅 [Azure Functions 文档](/azure/azure-functions/)，获取有关如何创建 Azure Functions 和发布 Functions 项目的详细指南。 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
