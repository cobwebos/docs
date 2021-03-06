---
title: Azure Functions Python 开发人员参考
description: 了解如何使用 Pythong 开发函数
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure Functions, 函数, 事件处理, 动态计算, 无服务体系结构, python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 97b954ee5e00c13211a3b2a2254b6d34bccb780c
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674941"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 开发人员指南

本文介绍了如何使用 Python 开发 Azure Functions。 以下内容假定你已阅读 [Azure Functions 开发人员指南](functions-reference.md)。 

有关 Python 中的独立函数示例项目，请参阅[Python 函数示例](/samples/browse/?products=azure-functions&languages=python)。 

## <a name="programming-model"></a>编程模型

Azure Functions 要求函数在 Python 脚本中作为无状态方法来处理输入并生成输出。 默认情况下，运行时需要将方法实现为 `__init__.py` 文件中 `main()` 名为的全局方法。 还可以[指定备用入口点](#alternate-entry-point)。

通过使用*函数 json*文件中定义的 `name` 属性，可通过方法属性将触发器和绑定中的数据绑定到函数。 例如，下面的_函数。 json_描述了一个简单的函数，该函数由名为 `req` 的 HTTP 请求触发：

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` 文件包含以下函数代码：

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

你还可以使用 Python 类型注释在函数中显式声明属性类型和返回类型。 这有助于你使用由许多 Python 代码编辑器提供的 intellisense 和自动完成功能。

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

使用 [ azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 包中附带的 Python 注释将输入和输出绑定到方法。

## <a name="alternate-entry-point"></a>备用入口点

您可以根据需要指定函数的默认行为，方法是在*函数 json*文件中指定 `scriptFile` 和 `entryPoint` 属性。 例如，下面的_函数_会通知运行时在_main.py_文件中使用 `customentry()` 方法，作为 Azure 函数的入口点。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>文件夹结构

Python 函数项目的文件夹结构类似于以下示例：

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

存在共享的 [host.json](functions-host-json.md) 文件，可用于配置函数应用。 每个函数都有自己的代码文件和绑定配置文件 (function.json)。 

共享代码应保留在单独的文件夹中。 若要引用 SharedCode 文件夹中的模块，可以使用以下语法：

```
from __app__.SharedCode import myFirstHelperFunction
```

若要引用函数的本地模块，可以使用相对导入语法，如下所示：

```
from . import example
```

将函数项目部署到 Azure 中的函数应用时， *FunctionApp*文件夹的整个内容应包含在包中，而不是文件夹本身。

## <a name="triggers-and-inputs"></a>触发器和输入

在 Azure Functions 中，输入分为两种类别：触发器输入和附加输入。 尽管它们在 `function.json` 文件中有所不同，但在 Python 代码中的用法是相同的。  当在 Azure 中运行时，触发器和输入源的连接字符串或机密会映射到 `local.settings.json` 文件中的值。 

例如，下面的代码演示了这两种情况之间的差异：

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

调用函数时，HTTP 请求作为 `req` 传递给函数。 将根据路由 URL 中的_ID_从 Azure Blob 存储中检索一个条目，并在函数体中将其作为 `obj` 提供。  此处指定的存储帐户是在中找到的连接字符串，它是函数应用使用的相同存储帐户。


## <a name="outputs"></a>Outputs

输出可以在返回值和输出参数中进行表示。 如果只有一个输出，则建议使用返回值。 对于多个输出，必须使用输出参数。

若要使用函数的返回值作为输出绑定的值，则绑定的 `name` 属性应在 `function.json` 中设置为 `$return`。

若要生成多个输出，请使用[`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python)接口提供的 `set()` 方法为绑定分配值。 例如，以下函数可以将消息推送到队列，还可返回 HTTP 响应。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>日志记录

可在函数应用中通过根 [`logging` ](https://docs.python.org/3/library/logging.html#module-logging) 处理程序来访问 Azure Functions 运行时记录器。 此记录器绑定到 Application Insights，并允许标记在函数执行期间遇到的警告和错误。

下面的示例在通过 HTTP 触发器调用函数时记录信息消息。

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

有其他日志记录方法可用于在不同跟踪级别向控制台进行写入：

| 方法                 | 描述                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | 在根记录器中写入具有 CRITICAL 级别的消息。  |
| **`error(_message_)`**   | 在根记录器中写入具有 ERROR 级别的消息。    |
| **`warning(_message_)`**    | 在根记录器中写入具有 WARNING 级别的消息。  |
| **`info(_message_)`**    | 在根记录器中写入具有 INFO 级别的消息。  |
| **`debug(_message_)`** | 在根记录器中写入具有 DEBUG 级别的消息。  |

若要了解有关日志记录的详细信息，请参阅[Monitor Azure Functions](functions-monitoring.md)。

## <a name="http-trigger-and-bindings"></a>HTTP 触发器和绑定

HTTP 触发器在函数 jon 文件中定义。 绑定的 `name` 必须与函数中的命名参数匹配。 在前面的示例中，使用了一个绑定名称 `req`。 此参数是一个[HttpRequest]对象，并返回一个[httpresponse.cache]对象。

通过[HttpRequest]对象，可以获取请求标头、查询参数、路由参数和消息正文。 

下面的示例来自 Python 的[HTTP 触发器模板](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)。 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

在此函数中，`name` 查询参数的值是从[HttpRequest]对象的 `params` 参数获取的。 使用 `get_json` 方法读取 JSON 编码的消息正文。 

同样，也可以在返回的[httpresponse.cache]对象中设置响应消息的 `status_code` 和 `headers`。

## <a name="concurrency"></a>并发

默认情况下，Python 运行时一次只能处理一个函数调用。 在下列一种或多种情况下，此并发级别可能不足：

+ 正在尝试处理同时进行的多个调用。
+ 正在处理大量 i/o 事件。
+ 您的应用程序受 i/o 限制。

在这些情况下，你可以通过异步运行和使用多个语言工作进程来提高性能。  

### <a name="async"></a>异步

建议使用 `async def` 语句，使函数以异步协同程序运行。

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

当 `main()` 函数同步时（没有 `async` 限定符），该函数将自动在 `asyncio` 线程池中运行。

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>使用多个语言辅助进程

默认情况下，每个函数主机实例都有一个语言辅助进程。 但是，对于每个主机实例，都支持具有多个语言工作进程。 然后，可以在这些语言工作进程之间平均分配函数调用。 使用[FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count)应用程序设置更改此值。 

## <a name="context"></a>上下文

若要在执行过程中获取函数的调用上下文，请在其签名中包含[`context`](/python/api/azure-functions/azure.functions.context?view=azure-python)参数。 

例如：

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**上下文**](/python/api/azure-functions/azure.functions.context?view=azure-python)类具有以下字符串属性：

`function_directory`  
在其中运行函数的目录。

`function_name`  
函数的名称。

`invocation_id`  
当前函数调用的 ID。

## <a name="global-variables"></a>全局变量

不保证应用的状态将保留以供将来执行。 不过，Azure Functions 运行时通常会对同一应用的多个执行重复运行相同的过程。 为了缓存昂贵计算的结果，请将其声明为全局变量。 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>环境变量

在函数中，[应用程序设置](functions-app-settings.md)（如服务连接字符串）在执行过程中公开为环境变量。 可以通过声明 `import os` 然后使用 `setting = os.environ["setting-name"]` 来访问这些设置。

下面的示例获取[应用程序设置](functions-how-to-use-azure-function-app-settings.md#settings)，其密钥名为 `myAppSetting`：

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

对于本地开发，应用程序设置将[保留在本地的设置文件中](functions-run-local.md#local-settings-file)。  

## <a name="python-version-and-package-management"></a>Python 版本和包管理

当前，Azure Functions 仅支持 Python 3.6.x（CPython 正式分发版）。

在使用 Azure Functions Core Tools 或 Visual Studio Code 进行本地开发时，将所需包的名称和版本添加到 `requirements.txt` 文件并使用 `pip` 安装它们。

例如，可以使用以下要求文件和 pip 命令从 PyPI 安装 `requests` 包。

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>发布到 Azure

准备好发布时，请确保所有依赖项都列在 "*要求" .txt*文件中，该文件位于项目目录的根目录下。 Azure Functions 可以[远程生成](functions-deployment-technologies.md#remote-build)这些依赖项。

从发布中排除的项目文件和文件夹（包括虚拟环境文件夹）将在 funcignore 文件中列出。 

默认情况下，VS Code 的[Azure Functions Core Tools](functions-run-local.md#v2)和[Azure Functions 扩展](functions-create-first-function-vs-code.md#publish-the-project-to-azure)将执行远程生成。 例如，使用以下命令：

```bash
func azure functionapp publish <app name>
```

如果你想要在本地而不是在 Azure 中生成应用，请在本地计算机上[安装 Docker](https://docs.docker.com/install/) ，并运行以下命令以使用[Azure Functions Core Tools](functions-run-local.md#v2) （func）进行发布。 请记住将 `<app name>` 替换为 Azure 中的函数应用名称。 

```bash
func azure functionapp publish <app name> --build-native-deps
```

实际上，Core Tools 会使用 docker 将 [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) 映像作为本地计算机上的容器来运行。 在此环境中，它随后会构建并安装源分发中所需的模块，然后将它们打包，以便最终部署到 Azure。

若要构建依赖项并使用持续交付（CD）系统发布，请[使用 Azure Pipelines](functions-how-to-azure-devops.md)。 

## <a name="unit-testing"></a>单元测试

使用标准测试框架，可以像使用其他 Python 代码那样测试使用 Python 编写的函数。 对于大多数绑定，可以通过从 `azure.functions` 包创建适当类的实例来创建 mock 输入对象。 由于[`azure.functions`](https://pypi.org/project/azure-functions/)包不会立即可用，因此请务必通过你的 `requirements.txt` 文件进行安装，如上面的 " [Python 版本和包管理](#python-version-and-package-management)" 一节中所述。

例如，下面是 HTTP 触发函数的模拟测试：

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
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

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
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
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

下面是另一个示例，其中包含队列触发的函数：

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```

## <a name="known-issues-and-faq"></a>已知问题和常见问题解答

所有已知问题和功能请求都使用 [GitHub 问题](https://github.com/Azure/azure-functions-python-worker/issues)列表进行跟踪。 如果遇到 GitHub 中未列出的问题，请打开“新问题”并提供问题的详细说明。

### <a name="cross-origin-resource-sharing"></a>跨域资源共享

Azure Functions 支持跨域资源共享（CORS）。 CORS 是[在门户中](functions-how-to-use-azure-function-app-settings.md#cors)和通过[Azure CLI](/cli/azure/functionapp/cors)配置的。 CORS 允许的源列表应用于 function app 级别。 启用 CORS 后，响应包括 `Access-Control-Allow-Origin` 标头。 有关详细信息，请参阅 [跨域资源共享](functions-how-to-use-azure-function-app-settings.md#cors)。

Python function apps[目前不支持](https://github.com/Azure/azure-functions-python-worker/issues/444)允许的源列表。 由于此限制，你必须在 HTTP 函数中明确设置 `Access-Control-Allow-Origin` 标头，如以下示例中所示：

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

确保还更新了函数 json，以支持 OPTIONS HTTP 方法：

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Chrome 浏览器使用此方法来协商允许的来源列表。 

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [Azure Functions 包 API 文档](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [Blob 存储绑定](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 绑定](functions-bindings-http-webhook.md)
* [存储绑定](functions-bindings-storage-queue.md)
* [计时器触发器](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[Httpresponse.cache]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
