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
ms.openlocfilehash: d25082c429c58c074726c75f7ff6f248daee4151
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050616"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 开发人员指南

本文介绍了如何使用 Python 开发 Azure Functions。 以下内容假定你已阅读 [Azure Functions 开发人员指南](functions-reference.md)。

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>编程模型

Azure 函数应是 Python 脚本中处理输入并生成输出的无状态方法。 默认情况下，运行时需要作为一个名为的全局方法实现的方法`main()`在`__init__.py`文件。

可以通过指定更改默认配置`scriptFile`并`entryPoint`中的属性*function.json*文件。 例如， _function.json_下面指示运行时使用`customentry()`中的方法_main.py_文件中，作为 Azure 函数的入口点。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

从触发器和绑定的数据绑定到该函数通过使用方法属性`name`中定义的属性*function.json*文件。 例如， _function.json_下面介绍由名为 HTTP 请求触发的简单函数`req`:

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

（可选） 若要利用 intellisense 和代码编辑器所提供的自动完成功能，可以还声明的属性类型和返回类型中使用 Python 类型批注的函数。 

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

使用 [ azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 包中附带的 Python 注释将输入和输出绑定到方法。

## <a name="folder-structure"></a>文件夹结构

Python 函数项目的文件夹结构如下所示：

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
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

函数将项目部署到 Azure 中的整个内容中的函数应用时*FunctionApp*文件夹应包含在包，但不是文件夹本身。

## <a name="triggers-and-inputs"></a>触发器和输入

在 Azure Functions 中，输入分为两种类别：触发器输入和附加输入。 尽管它们是在不同`function.json`文件时，使用情况是在 Python 代码中完全相同。  连接字符串或机密的触发器和输入源映射到值中`local.settings.json`文件在本地，运行时和在 Azure 中运行时的应用程序设置。 

例如，下面的代码演示了两者之间的差异：

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

调用函数时，HTTP 请求作为 `req` 传递给函数。 一个条目将从根据 Azure Blob 存储中检索_ID_中的路由 URL，并可作为`obj`函数体中。  此处指定存储帐户中找到连接字符串`AzureWebJobsStorage`这是由 function app 使用的同一存储帐户。


## <a name="outputs"></a>Outputs

输出可以在返回值和输出参数中进行表示。 如果只有一个输出，则建议使用返回值。 对于多个输出，必须使用输出参数。

若要使用函数的返回值作为输出绑定的值，则绑定的 `name` 属性应在 `function.json` 中设置为 `$return`。

若要生成多个输出，请使用 `azure.functions.Out` 接口提供的 `set()` 方法将值分配给绑定。 例如，以下函数可以将消息推送到队列，还可返回 HTTP 响应。

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
| logging.**critical(_message_)**   | 在根记录器中写入具有 CRITICAL 级别的消息。  |
| logging.**error(_message_)**   | 在根记录器中写入具有 ERROR 级别的消息。    |
| logging.**warning(_message_)**    | 在根记录器中写入具有 WARNING 级别的消息。  |
| logging.**info(_message_)**    | 在根记录器中写入具有 INFO 级别的消息。  |
| logging.**debug(_message_)** | 在根记录器中写入具有 DEBUG 级别的消息。  |

## <a name="async"></a>异步

我们建议编写 Azure 函数与异步协同例程使用`async def`语句。

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

如果是同步的 main （） 函数 (没有`async`限定符) 中自动运行该函数`asyncio`线程池。

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>上下文

若要在执行过程中获取函数的调用上下文，请在其签名中包含 `context` 参数。 

例如：

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

**Context** 类具有以下方法：

`function_directory`  
在其中运行函数的目录。

`function_name`  
函数的名称。

`invocation_id`  
当前函数调用的 ID。

## <a name="global-variables"></a>全局变量

不保证您的应用程序的状态将被保留以便在将来执行。 但是，Azure Functions 运行时通常会重用相同的多个执行的相同应用的过程。 若要缓存的昂贵的计算结果，请将其声明为全局变量。 

```python
CACHED_DATA = None

def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

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

如果你已准备好发布，请确保所有依赖项所示*requirements.txt*文件，它是位于项目目录的根目录。 如果使用的包需要编译器并且不支持从 PyPI 安装 manylinux 兼容滚轮的包，则发布到 Azure 会失败并出现以下错误： 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

若要自动生成并配置所需二进制文件，请在本地计算机上[安装 Docker](https://docs.docker.com/install/)并运行以下命令以使用 [Azure Functions Core Tools](functions-run-local.md#v2) (func) 进行发布。 请记住将 `<app name>` 替换为 Azure 中的函数应用名称。 

```bash
func azure functionapp publish <app name> --build-native-deps
```

实际上，Core Tools 会使用 docker 将 [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) 映像作为本地计算机上的容器来运行。 它随后会使用此环境从源分发版生成并安装所需模块，然后再打包它们以便最终部署到 Azure。

生成依赖项并使用持续交付 (CD) 系统进行发布[使用 Azure DevOps 管道](https://docs.microsoft.com/azure/azure-functions/functions-how-to-azure-devops)。 

## <a name="unit-testing"></a>单元测试

可以像使用标准的测试框架的其他 Python 代码进行测试以 Python 编写的函数。 对于大多数绑定，则可以通过创建从相应的类的实例来创建一个模拟的输入的对象`azure.functions`包。

例如，下面是函数的 HTTP 触发的模拟测试：

```python
# myapp/__init__.py
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/my_function', 
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(), 
            'Hello, Test!',
        )
```

下面是另一个示例中，与队列触发的函数：

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

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [Blob 存储绑定](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 绑定](functions-bindings-http-webhook.md)
* [存储绑定](functions-bindings-storage-queue.md)
* [计时器触发器](functions-bindings-timer.md)
