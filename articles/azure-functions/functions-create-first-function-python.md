---
title: 在 Azure Functions 中为 HTTP 请求创建无服务器 Python 函数
description: 使用 Azure Functions 创建无服务器 Python 代码并将其部署到云中。
ms.date: 02/11/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: a781e10cee4cf433de5e837490d901020a875205
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157884"
---
# <a name="quickstart-create-a-python-function-in-azure-that-responds-to-http-requests"></a>快速入门：在 Azure 中创建用于响应 HTTP 请求的 Python 函数

在本文中，你将使用命令行工具创建响应 HTTP 请求的 Python 函数。 在本地测试代码后，将代码部署到 Azure Functions 的无服务器环境。 完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还提供了[基于 Visual Studio 代码的版本](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-python)。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 或更高版本。
- [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 或更高版本。 
- [Python 3.7.4 - 64 位](https://www.python.org/downloads/release/python-374/)。 （Python 3.7.4 已在 Azure Functions 中进行验证；目前尚不支持 Python 3.8 和更高版本。）

### <a name="prerequisite-check"></a>先决条件检查

1. 在终端或命令窗口中，运行 `func --version` 检查 Azure Functions Core Tools 的版本是否为 2.7.1846 或以上。
1. 运行 `az --version` 检查 Azure CLI 版本是否为 2.0.76 或以上。
1. 运行 `az login` 登录到 Azure 并验证活动订阅。
1. 运行 `python --version` (Linux/MacOS) 或 `py --version` (Windows)，检查报告的 Python 版本是否为 3.7.x。

## <a name="create-and-activate-a-virtual-environment"></a>创建并激活虚拟环境

在适当的文件夹中，运行以下命令以创建并激活一个名为 `.venv` 的虚拟环境。 请务必使用受 Azure Functions 支持的 Python 3.7。


# <a name="bashtabbash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

如果 Python 未在 Linux 分发版中安装 venv 包，请运行以下命令：

```bash
sudo apt-get install python3-venv
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

所有后续命令将在这个已激活的虚拟环境中运行。 （若要退出虚拟环境，请运行 `deactivate`。）

## <a name="create-a-local-function-project"></a>创建本地函数项目

在 Azure Functions 中，有一个函数项目是一个或多个单独函数（每个函数响应特定的触发器）的容器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

1. 在虚拟环境中运行 `func init` 命令，以在某个文件夹中创建名为 *LocalFunctionProj* 的包含指定运行时的函数项目：

    ```
    func init LocalFunctionProj --python
    ```
    
    此文件夹包含项目的各个文件，其中包括名为 [local.settings.json](functions-run-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 由于 *local.settings.json* 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 *.gitignore* 文件的源代码管理中排除。

    > [!TIP]
    > 由于函数项目绑定到特定的运行时，因此必须使用相同的语言编写项目中的所有函数。

1. 导航到项目文件夹：

    ```
    cd LocalFunctionProj
    ```
    
1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数的唯一名称，`--template` 参数指定该函数的触发器。 `func new` 创建一个与函数名称匹配的、包含项目所选语言适用的代码文件的子文件夹，以及一个名为 *function.json* 的配置文件。

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>（可选）检查文件内容

如果需要，可以跳到[在本地运行函数](#run-the-function-locally)并稍后再检查文件内容。

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* 包含一个根据 *function.json* 中的配置触发的 `main()` Python 函数。

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

对于 HTTP 触发器，该函数将接收 *function.json* 中定义的变量 `req` 中的请求数据。 `req` 是 [azure.functions.HttpRequest 类](/python/api/azure-functions/azure.functions.httprequest)的实例。 在 *function.json* 中定义为 `$return` 的返回对象是 [azure.functions.HttpResponse 类](/python/api/azure-functions/azure.functions.httpresponse)的一个实例。 要了解详细信息，请参阅 [Azure Functions HTTP 触发器和绑定](functions-bindings-http-webhook.md)。

#### <a name="functionjson"></a>function.json

*function.json* 是一个配置文件，其中定义了函数的输入和输出 `bindings`，包括触发器类型。 如果需要，可以更改 `scriptFile` 以调用不同的 Python 文件。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

每个绑定都需要一个方向、类型和唯一的名称。 HTTP 触发器具有类型为 [`httpTrigger`](functions-bindings-http-webhook.md#trigger) 的输入绑定和类型为 [`http`](functions-bindings-http-webhook.md#output) 的输出绑定。


## <a name="run-the-function-locally"></a>在本地运行函数

通过启动 *LocalFunctionProj* 文件夹中的本地 Azure Functions 运行时主机来启动函数：

```
func start
```

应显示以下输出。 （如果 HttpExample 未按如下所示出现，原因可能是从 *HttpExample* 文件夹中启动了主机。 在这种情况下，请按 **Ctrl**+**C** 停止主机，导航到父级 *LocalFunctionProj* 文件夹，然后再次运行 `func start`。）

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

将此输出中 `HttpExample` 函数的 URL 复制到浏览器，并追加查询字符串 `?name=<your-name>`，使完整 URL 类似于 `http://localhost:7071/api/HttpExample?name=Functions`。 浏览器应会显示类似于 `Hello Functions` 的消息：

![在浏览器中本地运行函数后的结果](./media/functions-create-first-function-python/function-test-local-browser.png)

当你发出请求时，运行 `func start` 所在的终端还会显示日志输出。

准备就绪后，按 **Ctrl**+**C** 停止函数宿主。

## <a name="create-supporting-azure-resources-for-your-function"></a>创建函数的支持性 Azure 资源

在将函数代码部署到 Azure 之前，需要创建三个资源：

- 一个资源组：相关资源的逻辑容器。
- 一个 Azure 存储帐户：维护有关项目的状态和其他信息。
- 一个 Azure Functions 应用：提供用于执行函数代码的环境。 函数应用映射到本地函数项目，可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

使用 Azure CLI 命令创建这些项。 完成后，每个命令将提供 JSON 输出。

1. 使用 [az login](/cli/azure/reference-index#az-login) 命令登录到 Azure（如果尚未这样做）：

    ```azurecli
    az login
    ```
    
1. 使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 以下示例在 `westeurope` 区域中创建名为 `AzureFunctionsQuickstart-rg` 的资源组。 （通常，你会在 `az account list-locations` 命令输出的、与你靠近的某个可用区域中创建资源组和资源。）

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > 不能在同一资源组中托管 Linux 和 Windows 应用。 如果名为 `AzureFunctionsQuickstart-rg` 的现有资源组有 Windows 函数应用或 Web 应用，必须使用其他资源组。
    
1. 使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令在资源组和区域中创建常规用途存储帐户。 在以下示例中，请将 `<storage_name>` 替换为适合自己的全局唯一名称。 名称只能包含 3 到 24 个数字和小写字母字符。 `Standard_LRS` 指定典型的常规用途帐户。

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    在本快速入门中使用的存储帐户只会产生几美分的费用。
    
1. 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令创建 Functions 应用。 在以下示例中，请将 `<storage_name>` 替换为在上一步骤中使用的帐户的名称，并将 `<app_name>` 替换为适合自己的全局唯一名称。 `<app_name>` 也是函数应用的默认 DNS 域。

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    此命令将创建一个函数应用，该应用运行 [Azure Functions 消耗计划](functions-scale.md#consumption-plan)中指定的语言运行时，根据本教程产生的用量，此操作是免费的。 该命令还会在同一资源组中预配关联的 Azure Application Insights 实例，可以使用它来监视函数应用和查看日志。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 该实例在激活之前不会产生费用。
    
## <a name="deploy-the-function-project-to-azure"></a>将函数项目部署到 Azure

准备好所需的资源后，便可以使用 [func azure functionapp publish](functions-run-local.md#project-file-deployment) 命令将本地函数项目部署到 Azure 中的函数应用。 在以下示例中，请将 `<app_name>` 替换为你的应用的名称。

```
func azure functionapp publish <app_name>
```

如果看到错误“找不到名为...的应用”，请等待几秒后重试，因为在执行上一 `az functionapp create` 命令后，Azure 可能尚未完全初始化该应用。

publish 命令显示类似于以下输出的结果（为简洁起见，示例中的结果已截断）：

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>在 Azure 上调用函数

由于函数使用 HTTP 触发器，因此，可以通过在浏览器中或使用 curl 等工具，向此函数的 URL 发出 HTTP 请求来调用它。 在这两种情况下，`code` URL 参数都是唯一的函数密钥，用于授权函数终结点的调用。

# <a name="browsertabbrowser"></a>[浏览器](#tab/browser)

将 publish 命令的输出中显示的完整“调用 URL”复制到浏览器的地址栏，并追加查询参数 `&name=Azure`。  浏览器显示的输出应与本地运行函数时显示的输出类似。

![在 Azure 上运行函数后浏览器中的输出](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[curl](#tab/curl)

结合“调用 URL”运行 [curl](https://curl.haxx.se/)，并追加参数 `&name=Azure`。  该命令的输出应是文本“Hello Azure”。

![使用 curl 在 Azure 上运行函数后的输出](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> 若要查看已发布的 Python 应用的近乎实时日志，请使用 [Application Insights 实时指标流](functions-monitoring.md#streaming-logs)。

## <a name="clean-up-resources"></a>清理资源

若要继续执行下一步骤[添加 Azure 存储队列输出绑定](functions-add-output-binding-storage-queue-python.md)，请保留到目前为止构建的所有资源。

否则，请使用以下命令删除资源组及其包含的所有资源，以免产生额外的费用。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 Azure 存储队列输出绑定](functions-add-output-binding-storage-queue-python.md)
