---
title: 在 Azure 中创建 HTTP 触发的 Python 函数
description: 了解如何使用 Azure Functions Core Tools 和 Azure CLI 在 Azure 中创建你的第一个 Python 函数。
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 791348088d909785b36934c3b9a2ae00fc0acbb7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622034"
---
# <a name="create-an-http-triggered-python-function-in-azure"></a>在 Azure 中创建 HTTP 触发的 Python 函数

本文介绍如何使用命令行工具创建在 Azure Functions 中运行的 Python 项目。 另外还介绍如何创建由 HTTP 请求触发的函数。 最后，发布项目，在 Azure 中作为[无服务器函数](functions-scale.md#consumption-plan)运行。

本文是 Azure Functions 的两个 Python 快速入门之一。 完成本快速入门后，可将[ Azure 存储队列输出绑定添加](functions-add-output-binding-storage-queue-python.md)到你的函数。

## <a name="prerequisites"></a>先决条件

开始之前，必须：

+ 安装 [Python 3.6.8](https://www.python.org/downloads/)。 此版本的 Python 已使用 Functions 进行验证。 尚不支持 3.7 和更高版本。

+ 安装 [Azure Functions Core Tools](./functions-run-local.md#v2) 版本 2.7.1575 或更高版本。

+ 安装 [Azure CLI](/cli/azure/install-azure-cli) 版本 2.x 或更高版本。

+ 拥有一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>创建并激活虚拟环境（可选）

应该使用 Python 3.6.x 环境在本地开发 Python 函数。 运行以下命令来创建并激活一个名为 `.venv` 的虚拟环境。

> [!NOTE]
> 如果 Python 未在 Linux 分发版上安装 venv，则可以使用以下命令安装它：
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash：

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell 或 Windows 命令提示符：

```powershell
py -m venv .venv
.venv\scripts\activate
```

激活虚拟环境以后，即可在其中运行剩下的命令。 若要脱离虚拟环境，请运行 `deactivate`。

## <a name="create-a-local-functions-project"></a>创建本地函数项目

函数项目等效于 Azure 中的函数应用。 它可能具有多个函数，这些函数具有相同的本地和托管配置。

1. 在虚拟环境中，运行以下命令：

    ```console
    func init MyFunctionProj
    ```

1. 选择“python”作为辅助角色运行时  。

    此命令创建一个 _MyFunctionProj_ 文件夹。 它包含以下三个文件：

    * *local.settings.json*：用于在本地运行时存储应用设置和连接字符串。 此文件不会被发布到 Azure。
    * *requirements.txt*：包含在发布到 Azure 时系统要安装的包列表。
    * *host.json*：包含在函数应用中影响所有函数的全局配置选项。 此文件会被发布到 Azure。

1. 转到新的 *MyFunctionProj* 文件夹：

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>创建函数

将函数添加到新项目。

1. 若要将函数添加到项目中，请运行以下命令：

    ```console
    func new
    ```

1. 使用向下箭头选择“HTTP 触发器”模板。 

1. 当系统提示你输入函数名称时，请输入 *HttpTrigger*，然后按 Enter。

这些命令创建名为 _HttpTrigger_ 的子文件夹。 它包含以下文件：

* function.json：  定义函数、触发器和其他绑定的配置文件。 请注意，在此文件中，`scriptFile` 的值指向包含函数的文件，而 `bindings` 数组则定义调用触发器和绑定。

    每个绑定都需要一个方向、类型和唯一名称。 HTTP 触发器具有类型为 [`httpTrigger`](functions-bindings-http-webhook.md#trigger) 的输入绑定和类型为 [`http`](functions-bindings-http-webhook.md#output) 的输出绑定。

* *\_\_init\_\_.py*：作为 HTTP 触发函数的脚本文件。 请注意，此脚本有默认的 `main()`。 触发器中的 HTTP 数据使用 `req` 命名`binding parameter`传递到此函数。 根据 function.json 中的定义，`req` 是 [azure.functions.HttpRequest class](/python/api/azure-functions/azure.functions.httprequest) 的一个实例。 

    在 *function.json* 中定义为 `$return` 的返回对象是 [azure.functions.HttpResponse 类](/python/api/azure-functions/azure.functions.httpresponse)的一个实例。 要了解详细信息，请参阅 [Azure Functions HTTP 触发器和绑定](functions-bindings-http-webhook.md)。

## <a name="run-the-function-locally"></a>在本地运行函数

此函数使用 Azure Functions 运行时在本地运行。

1. 此命令启动函数应用：

    ```console
    func host start
    ```

    Azure Functions 主机启动时，会写入类似于以下输出的内容。 在这里，为了可读性，这些输出已经被截断：

    ```output
    
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %
    
    ...
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. 从运行时输出中复制 `HttpTrigger` 函数的 URL，将其粘贴到浏览器的地址栏中。

1. 将查询字符串 `?name=<yourname>` 追加到此 URL 并执行请求。 以下屏幕截图演示本地函数返回到浏览器的对 GET 请求的响应：

    ![在浏览器中进行本地验证](./media/functions-create-first-function-python/function-test-local-browser.png)

1. 选择 Ctrl+C，关闭函数应用。

在本地运行函数以后，即可在 Azure 中创建函数应用和其他必需的资源。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>在 Azure 中创建一个函数应用

函数应用提供了用于执行函数代码的环境。 可以使用它将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

运行以下命令。 将 `<APP_NAME>` 替换为唯一函数应用名称。 将 `<STORAGE_NAME>` 替换为存储帐户名称。 `<APP_NAME>` 也是函数应用的默认 DNS 域。 此名称在 Azure 的所有应用中必须独一无二。

> [!NOTE]
> 不能在同一资源组中托管 Linux 和 Windows 应用。 如果名为 `myResourceGroup` 的现有资源组有 Windows 函数应用或 Web 应用，必须使用其他资源组。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

上述命令还在同一资源组中预配关联的 Azure Application Insights 实例。 可以使用该实例来监视函数应用并查看日志。

现在，可以将本地函数项目发布到 Azure 中的函数应用了。

## <a name="deploy-the-function-app-project-to-azure"></a>将函数应用项目部署到 Azure

在 Azure 中创建函数应用后，可以使用 [func azure functionapp publish](functions-run-local.md#project-file-deployment) Core Tools 命令将项目代码部署到 Azure。 在此示例中，使用应用名称替代 `<APP_NAME>`。

```console
func azure functionapp publish <APP_NAME> --build remote
```

`--build remote` 选项通过部署包中的文件在 Azure 中远程生成 Python 项目。 

将会看到类似于以下消息的输出。 在这里，为了可读性，这些输出已经被截断：

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

可以复制 `HttpTrigger` 的 `Invoke url` 值，并使用它在 Azure 中验证函数。 此 URL 包含 `code` 查询字符串值，该值是一个函数密钥，因此其他人难以在 Azure 中调用你的 HTTP 触发器终结点。

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> 若要查看已发布的 Python 应用的近乎实时日志，请使用 [Application Insights 实时指标流](functions-monitoring.md#streaming-logs)。

## <a name="next-steps"></a>后续步骤

已使用 HTTP 触发函数创建 Python 函数项目，在本地计算机上运行该项目，并将其部署到 Azure。 现在，通过以下方式扩展函数

> [!div class="nextstepaction"]
> [添加 Azure 存储队列输出绑定](functions-add-output-binding-storage-queue-python.md)
