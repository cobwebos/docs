---
title: 将 Azure 存储队列绑定添加到 Python 函数
description: 了解如何使用 Azure CLI 和 Functions Core Tools 将 Azure 存储队列输出绑定添加到 Python 函数。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 9fdbf3466256c5e24de17541770fa2095fcf38a4
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171086"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>将 Azure 存储队列绑定添加到 Python 函数

无需编写自己的集成代码，即可使用 Azure Functions 将 Azure 服务和其他资源连接到函数。 这些绑定表示输入和输出，在函数定义中声明。  绑定中的数据作为参数提供给函数。 触发器是一种特殊类型的输入绑定。  尽管一个函数只有一个触发器，但它可以有多个输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

本文介绍如何将[前一篇快速入门文章](functions-create-first-function-python.md)中创建的函数与 Azure 存储队列相集成。 添加到此函数的输出绑定会将 HTTP 请求中的数据写入到队列中的消息。

大多数绑定都需要一个存储的连接字符串，函数将使用该字符串来访问绑定的服务。 为便于建立此连接，请使用连同函数应用一起创建的存储帐户。 与此帐户建立的连接已存储在名为 `AzureWebJobsStorage` 的应用设置中。  

## <a name="prerequisites"></a>先决条件

在开始学习本文之前，请完成 [Python 快速入门第 1 部分](functions-create-first-function-python.md)中的步骤。

## <a name="download-the-function-app-settings"></a>下载函数应用设置

在前一篇快速入门文章中，你已在 Azure 中创建了一个函数应用以及所需的存储帐户。 此帐户的连接字符串安全存储在 Azure 中的应用设置内。 在本文中，你要将消息写入到同一帐户中的存储队列。 若要在本地运行函数时连接到该存储帐户，必须将应用设置下载到 local.settings.json 文件。 运行以下 Azure Functions Core Tools 命令，将设置下载到 local.settings.json（请将 `<APP_NAME>` 替换为前一篇文章中的函数应用名称）：

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

可能需要登录到 Azure 帐户。

> [!IMPORTANT]  
> 由于 local.settings.json 文件包含机密，因此永远不会发布它，应将其从源代码管理中排除。

需要 `AzureWebJobsStorage` 值，即存储帐户连接字符串。 你将使用此连接来验证输出绑定是否按预期方式工作。

## <a name="enable-extension-bundles"></a>启用扩展捆绑包

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

现在，可将存储输出绑定添加到项目。

## <a name="add-an-output-binding"></a>添加输出绑定

在 Functions 中，每种类型的绑定都需要一个 `direction`、`type`，以及要在 function.json 文件中定义的唯一 `name`。 根据绑定类型，可能还需要其他属性。 [队列输出配置](functions-bindings-storage-queue.md#output---configuration)描述 Azure 存储队列绑定所需的字段。

若要创建绑定，请将绑定配置对象添加到 function.json 文件。 编辑 HttpTrigger 文件夹中的 function.json 文件，以将一个包含以下属性的对象添加到 `bindings` 数组：

| 属性 | 值 | 说明 |
| -------- | ----- | ----------- |
| **`name`** | `msg` | 用于标识代码中引用的绑定参数的名称。 |
| **`type`** | `queue` | 该绑定是 Azure 存储队列绑定。 |
| **`direction`** | `out` | 该绑定是输出绑定。 |
| **`queueName`** | `outqueue` | 绑定要写入到的队列的名称。 如果 `queueName` 不存在，首次使用绑定时，它会创建该属性。 |
| **`connection`** | `AzureWebJobsStorage` | 包含存储帐户连接字符串的应用设置的名称。 `AzureWebJobsStorage` 设置包含连同函数应用一起创建的存储帐户的连接字符串。 |

function.json 文件现在应如以下示例所示：

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

## <a name="add-code-that-uses-the-output-binding"></a>添加使用输出绑定的代码

配置 `name` 后，可以开始使用它来以函数签名中的方法属性的形式访问绑定。 在以下示例中，`msg` 是 [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) 的实例。

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

使用输出绑定时，无需使用 Azure 存储 SDK 代码进行身份验证、获取队列引用或写入数据。 Functions 运行时和队列输出绑定将为你执行这些任务。

## <a name="run-the-function-locally"></a>在本地运行函数

如前所述，使用以下命令在本地启动 Functions 运行时：

```bash
func host start
```

> [!NOTE]  
> 由于在前一篇快速入门中，你已在 host.json 中启用扩展捆绑包，因此在启动期间已下载并安装[存储绑定扩展](functions-bindings-storage-blob.md#packages---functions-2x)以及其他 Microsoft 绑定扩展。

从运行时输出中复制 `HttpTrigger` 函数的 URL，将其粘贴到浏览器的地址栏中。 将查询字符串 `?name=<yourname>` 追加到此 URL 并运行请求。 与在前一篇文章中一样，浏览器中应会显示相同的响应。

这一次，输出绑定还会在存储帐户中创建名为 `outqueue` 的队列，并添加包含此字符串的消息。

接下来，使用 Azure CLI 查看新队列，并检查是否已添加了一条消息。 也可以使用 [Microsoft Azure 存储资源管理器][Azure Storage Explorer]或 [Azure 门户](https://portal.azure.com)查看队列。

### <a name="set-the-storage-account-connection"></a>设置存储帐户连接

打开 local.settings.json 文件并复制 `AzureWebJobsStorage` 的值（即存储帐户连接字符串）。 使用以下 Bash 命令将 `AZURE_STORAGE_CONNECTION_STRING` 环境变量设置为该连接字符串：

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

在 `AZURE_STORAGE_CONNECTION_STRING` 环境变量中设置连接字符串时，无需每次都要提供身份验证，即可访问你的存储帐户。

### <a name="query-the-storage-queue"></a>查询存储队列

可以使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令查看帐户中的存储队列，如以下示例所示：

```azurecli-interactive
az storage queue list --output tsv
```

此命令的输出包含名为 `outqueue` 的队列，即运行函数时创建的队列。

接下来，使用 [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 命令查看此队列中的消息，如以下示例所示：

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

返回的字符串应与发送的用于测试函数的消息相同。

> [!NOTE]  
> 以上示例从 base64 解码返回的字符串。 这是因为，队列存储绑定以 [base64 字符串](functions-bindings-storage-queue.md#encoding)的形式写入和读取 Azure 存储。

现在，可将更新的函数应用重新发布到 Azure。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

同样，可以使用 cURL 或浏览器来测试已部署的函数。 与前面一样，请将查询字符串 `&name=<yourname>` 追加到 URL，如以下示例所示：

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

可以[检查存储队列消息](#query-the-storage-queue)，以再次确认输出绑定是否在队列中生成了新的消息。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

现已更新 HTTP 触发的函数，使其将数据写入存储队列。 若要详细了解如何使用 Python 开发 Azure Functions，请参阅 [Azure Functions Python 开发人员指南](functions-reference-python.md)及 [Azure Functions 触发器和绑定](functions-triggers-bindings.md)。 有关 Python 中完整 Function 项目的示例，请参阅 [Python Functions 示例](/samples/browse/?products=azure-functions&languages=python)。 

接下来，应为函数应用启用 Application Insights 监视：

> [!div class="nextstepaction"]
> [启用 Application Insights 集成](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/