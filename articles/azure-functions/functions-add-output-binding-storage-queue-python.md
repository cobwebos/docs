---
title: 将 Azure 存储队列绑定添加到 Python 函数
description: 使用输出绑定将 Azure 存储队列与 Python 函数相集成。
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: f5527e0e636c3f8c9ee3723570ed9811f0df3641
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198473"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>将 Azure 存储队列绑定添加到 Python 函数

本文介绍如何将 Azure 存储队列与在[创建 HTTP 触发的 Python 函数](functions-create-first-function-python.md)中创建的函数和存储帐户相集成。 可以使用一个输出绑定来实现这种集成。该绑定可将 HTTP 请求中的数据写入队列中的消息。  除了在前一篇快速入门中提到的几美分费用以外，完成本文不会产生其他费用。

## <a name="prerequisites"></a>必备条件

- 已完成快速入门[创建 HTTP 触发的 Python 函数](functions-create-first-function-python.md)。 如果在该文章结束时清理了资源，请再次执行相应的步骤以在 Azure 中重新创建 Functions 应用，但将资源保留原样。

## <a name="retrieve-the-azure-storage-connection-string"></a>检索 Azure 存储连接字符串

当你参考前一篇快速入门在 Azure 中创建函数应用时，同时已创建了一个存储帐户。 此帐户的连接字符串安全存储在 Azure 中的应用设置内。 将设置下载到 *local.settings.json* 文件中后，在本地运行函数时，可以在同一帐户中使用该连接写入存储队列。 

1. 在项目的根目录中运行以下命令（请将 `<app_name>` 替换为前一篇快速入门中所用的函数应用名称）。 此命令将覆盖该文件中的任何现有值。

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. 打开 *local.settings.json*，找到名为 `AzureWebJobsStorage` 的值，即存储帐户连接字符串。 在本文的其他部分，将使用名称 `AzureWebJobsStorage` 和该连接字符串。

> [!IMPORTANT]
> 由于 *local.settings.json* 包含从 Azure 下载的机密，因此请始终从源代码管理中排除此文件。 连同本地函数项目一起创建的 *.gitignore* 文件默认会排除该文件。

## <a name="add-an-output-binding-to-functionjson"></a>将输出绑定添加到 function.json

尽管一个函数只能有一个触发器，但它可以有多个输入和输出绑定，因此，你无需编写自定义集成代码，就能连接到其他 Azure 服务和资源。 在适用于函数所用语言的 function 文件夹中的 *function.json* 文件内声明这些绑定。

在前一篇快速入门中，*HttpExample* 文件夹中的 *function.json* 文件在 `bindings` 集合中包含两个绑定：

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

每个绑定至少有一个类型、一个方向和一个名称。 在以上示例中，第一个绑定的类型为 `httpTrigger`，方向为 `in`。 对于 `in` 方向，`name` 指定在触发器调用函数时，要发送到该函数的输入参数的名称。

第二个绑定的类型为 `http`，方向为 `out`，在这种情况下，`$return` 的特殊 `name` 指示此绑定使用函数的返回值，而不是提供输入参数。

若要从此函数写入 Azure 存储队列，请添加类型为 `queue`、名称为 `msg` 的 `out` 绑定，如以下代码所示：

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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

在这种情况下，`msg` 将作为输出参数提供给函数。 对于 `queue` 类型，还必须在 `queueName` 中指定队列的名称，并在 `connection` 中提供 Azure 存储连接的名称（来自 *local.settings.json*）。 

有关绑定的详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)和[队列输出配置](functions-bindings-storage-queue.md#output---configuration)。

## <a name="add-code-to-use-the-output-binding"></a>添加使用输出绑定的代码

在 *function.json* 中指定队列绑定后，可以更新函数，以接收 `msg` 输出参数并将消息写入队列。

按以下代码所示更新 *HttpExample\\\_\_init\_\_.py*，将 `msg` 参数添加到函数定义，并将 `msg.set(name)` 添加到 `if name:` 语句下。

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

`msg` 参数是 [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) 的实例。 其 `set` 方法将字符串消息写入队列，在本例中，此消息是在 URL 查询字符串中传递给函数的名称。

请注意，不需要编写任何用于身份验证、获取队列引用或写入数据的代码。  在 Azure Functions 运行时和队列输出绑定中可以方便地处理所有这些集成任务。

## <a name="run-and-test-the-function-locally"></a>在本地运行并测试函数

1. 在终端中或命令提示符下，导航到函数项目文件夹 *LocalFunctionProj*。

1. 使用以下命令启动 Azure Functions 运行时主机。

    ```
    func host start
    ```

1. 启动完成后，将会看到 `HttpExample` 终结点的 URL。请将此 URL 复制到浏览器并追加查询字符串 `?name=<your-name>`，使完整的 URL 类似于 `http://localhost:7071/api/HttpExample?name=Guido`。 与上一篇文章中一样，浏览器应会显示类似于 `Hello Guido` 的消息。

    如果未看到 `HttpExample` 终结点，请按 **Ctrl**+**C** 停止主机，然后检查输出中的错误。 例如，如果 *function.json* 中出现错误，则主机不会激活终结点。 另外，请检查 `func host start` 是否是从函数项目文件夹运行的，而不是从 *HttpExample* 文件夹运行的。

1. 完成后，按 **Ctrl**+**C** 停止主机。

> [!TIP]
> 在启动过程中，主机会下载并安装[存储绑定扩展](functions-bindings-storage-blob.md#add-to-your-functions-app)和其他 Microsoft 绑定扩展。 之所以安装这些扩展，是因为默认情况下，已在 *host.json* 文件中使用以下属性启用了绑定扩展：
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> 如果遇到任何与绑定扩展相关的错误，请检查上述属性是否在 *host.json* 中存在。

## <a name="view-the-message-in-the-azure-storage-queue"></a>查看 Azure 存储队列中的消息

当函数为 Web 浏览器生成 HTTP 响应时，它还会调用 `msg.set(name)`，以将消息写入队列绑定中指定的名为 `outqueue` 的 Azure 存储队列。 可以在 [Azure 门户](../storage/queues/storage-quickstart-queues-portal.md)或 [Microsoft Azure 存储资源管理器](https://storageexplorer.com/)中查看队列。 也可以按以下步骤中所述，在 Azure CLI 中查看队列：

1. 打开函数项目的 *local.setting.json* 文件，并复制连接字符串值。 在终端或命令窗口中运行以下命令，以创建名为 `AZURE_STORAGE_CONNECTION_STRING` 的环境变量（请在 `<connection_string>` 位置粘贴特定的连接字符串）。 （创建此环境变量后，无需在使用 `--connection-string` 参数的每个后续命令中提供连接字符串。）

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. （可选）使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令查看帐户中的存储队列。 此命令的输出应包含名为 `outqueue` 的队列，该队列是函数将其第一条消息写入该队列时创建的。
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. 使用 [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 命令查看此队列中的消息，队列名称应是前面在测试函数时使用的名称。 该命令检索队列中采用 [base64 编码](functions-bindings-storage-queue.md#encoding)的第一条消息，因此，还必须将此消息解码，才能以文本格式查看它。

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    由于需要取消引用消息集合并从 base64 解码，因此请运行 PowerShell 并使用 PowerShell 命令。

    ---
    
## <a name="redeploy-the-project-to-azure"></a>将项目重新部署到 Azure

在本地测试函数并验证它已将消息写入 Azure 存储队列后，接下来可以重新部署项目以更新 Azure 上运行的终结点。

1. 在 *LocalFunctionsProj* 文件夹中，使用 [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) 命令重新部署项目（请将 `<app_name>` 替换为你的应用的名称）。

    ```
    func azure functionapp publish <app_name>
    ```
    
1. 像在上一篇快速入门中一样，使用浏览器或 CURL 来测试重新部署的函数。

    # <a name="browser"></a>[浏览器](#tab/browser)
    
    将 publish 命令的输出中显示的完整“调用 URL”复制到浏览器的地址栏，并追加查询参数 `&name=Azure`。  浏览器显示的输出应与本地运行函数时显示的输出类似。

    ![在 Azure 上运行函数后浏览器中的输出](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    结合“调用 URL”运行 [curl](https://curl.haxx.se/)，并追加参数 `&name=Azure`。  该命令的输出应是文本“Hello Azure”。
    
    ![使用 curl 在 Azure 上运行函数后的输出](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. 按上一部分所述再次检查存储队列，验证它是否包含已写入到其中的新消息。

    如果使用 Azure CLI 检查该队列，`az storage message peek` 命令只会显示该队列中的第一条消息。 若要模拟消息处理，请改用 `az storage message get` 并添加所有相同的参数。 `get` 命令将返回消息，并将其从队列中删除。 然后，你可以重复同一个命令，直到队列已清空（此时该命令会返回错误）。

## <a name="clean-up-resources"></a>清理资源

若要继续执行下一步：[启用 Application Insights 集成](functions-monitoring.md#manually-connect-an-app-insights-resource)，请保留到目前为止构建的所有资源。

否则，请使用以下命令删除资源组及其包含的所有资源，以免产生额外的费用。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [启用 Application Insights 与 Azure Functions 应用的集成](functions-monitoring.md#manually-connect-an-app-insights-resource)

其他资源：

- [Python 中完整函数项目的示例](/samples/browse/?products=azure-functions&languages=python)。
- [Azure Functions Python 开发人员指南](functions-reference-python.md)
- [Azure Functions 触发器和绑定](functions-triggers-bindings.md)。
- [Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)
- [估算消耗计划成本](functions-consumption-costs.md)一文。
