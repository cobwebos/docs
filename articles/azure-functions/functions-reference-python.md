---
title: Azure Functions Python 开发人员参考
description: 了解如何使用 Pythong 开发函数
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 49577f5ac274b4e34fa07415e5495329ff650aa5
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83676193"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 开发人员指南

本文介绍了如何使用 Python 开发 Azure Functions。 以下内容假定你已阅读 [Azure Functions 开发人员指南](functions-reference.md)。

有关使用 Python 的独立函数示例项目，请参阅 [Python Functions 示例](/samples/browse/?products=azure-functions&languages=python)。

## <a name="programming-model"></a>编程模型

Azure Functions 要求函数是 Python 脚本中处理输入并生成输出的无状态方法。 默认情况下，运行时期望该方法在 `__init__.py` 文件中作为名为 `main()` 的全局方法实现。 还可以[指定备用入口点](#alternate-entry-point)。

来自触发器和绑定的数据使用在 function.json 文件中定义的 `name` 属性，通过方法特性绑定到函数。 例如，下面的 function.json 描述一个由名为 `req` 的 HTTP 请求触发的简单函数：

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

根据这一定义，包含函数代码的 `__init__.py` 文件可能类似于以下示例：

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

还可以使用 Python 类型注释在函数中显式声明属性类型和返回类型。 这有助于使用许多 Python 代码编辑器提供的 intellisense 和自动完成功能。

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

使用 [ azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 包中附带的 Python 注释将输入和输出绑定到方法。

## <a name="alternate-entry-point"></a>备用入口点

可以选择在 function.json 文件中指定 `scriptFile` 和 `entryPoint` 属性来更改函数的默认行为。 例如，下面的 function.json 指示运行时使用 main.py 文件中的 `customentry()` 方法作为 Azure 函数的入口点 。

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

Python 函数项目的建议文件夹结构如以下示例所示：

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 | - Dockerfile
 tests
```
主项目文件夹 (\_\_app\_\_) 可以包含以下文件：

* *local.settings.json*：用于在本地运行时存储应用设置和连接字符串。 此文件不会被发布到 Azure。 若要了解详细信息，请参阅 [local.settings.file](functions-run-local.md#local-settings-file)。
* requirements.txt：包含系统在发布到 Azure 时安装的包列表。
* *host.json*：包含在函数应用中影响所有函数的全局配置选项。 此文件会被发布到 Azure。 本地运行时，并非所有选项都受支持。 若要了解详细信息，请参阅 [host.json](functions-host-json.md)。
* .funcignore：（可选）声明不应发布到 Azure 的文件。
* .gitignore：（可选）声明从 git 存储库中排除的文件，如 local.settings.json。
* Dockerfile：（可选）在[自定义容器](functions-create-function-linux-custom-image.md)中发布项目时使用。

每个函数都有自己的代码文件和绑定配置文件 (function.json)。

在 Azure 中将项目配置到函数应用时，主项目 (\_\_app\_\_) 文件夹的整个内容应包含在包中，但不包含该文件夹本身。 在此示例 `tests` 中，建议你在独立于项目文件夹的文件夹中维护测试。 这样可防止对应用部署测试代码。 有关详细信息，请参阅[单元测试](#unit-testing)。

## <a name="import-behavior"></a>导入行为

可以使用显式相对引用和绝对引用在函数代码中导入模块。 根据以上所示的文件夹结构，以下导入在函数文件 \_\_app\_\_\my\_first\_function\\_\_init\_\_.py 中工作：

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

以下导入不在同一文件中工作：

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

共享代码应保留在 \_\_app\_\_ 下一个单独的文件夹中。 若要引用 shared\_code 文件夹中的模块，可以使用以下语法：

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>触发器和输入

在 Azure Functions 中，输入分为两种类别：触发器输入和附加输入。 虽然它们在 `function.json` 文件中并不相同，但它们在 Python 代码中的使用方法却是相同的。  在本地运行时，触发器和输入源的连接字符串或机密映射到 `local.settings.json` 文件中的值，而在 Azure 中运行时则映射到应用程序设置。

例如，以下代码演示两者之间的差异：

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

调用函数时，HTTP 请求作为 `req` 传递给函数。 将基于路由 URL 中的 ID 从 Azure Blob 存储检索一个条目，并在函数体中用作 `obj`。  此处指定的存储帐户是在 AzureWebJobsStorage 应用设置中找到的连接字符串，该帐户即函数应用使用的同一个存储帐户。


## <a name="outputs"></a>Outputs

输出可以在返回值和输出参数中进行表示。 如果只有一个输出，则建议使用返回值。 对于多个输出，必须使用输出参数。

若要使用函数的返回值作为输出绑定的值，则绑定的 `name` 属性应在 `function.json` 中设置为 `$return`。

若要生成多个输出，请使用 [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) 接口提供的 `set()` 方法将值分配给绑定。 例如，以下函数可以将消息推送到队列，还可返回 HTTP 响应。

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

可在函数应用中通过根 [`logging`](https://docs.python.org/3/library/logging.html#module-logging) 处理程序来访问 Azure Functions 运行时记录器。 此记录器绑定到 Application Insights，并允许标记在函数执行期间遇到的警告和错误。

下面的示例在通过 HTTP 触发器调用函数时记录信息消息。

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

有其他日志记录方法可用于在不同跟踪级别向控制台进行写入：

| 方法                 | 说明                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | 在根记录器中写入具有 CRITICAL 级别的消息。  |
| **`error(_message_)`**   | 在根记录器中写入具有 ERROR 级别的消息。    |
| **`warning(_message_)`**    | 在根记录器中写入具有 WARNING 级别的消息。  |
| **`info(_message_)`**    | 在根记录器中写入具有 INFO 级别的消息。  |
| **`debug(_message_)`** | 在根记录器中写入具有 DEBUG 级别的消息。  |

若要详细了解日志记录，请参阅[监视 Azure Functions](functions-monitoring.md)。

## <a name="http-trigger-and-bindings"></a>HTTP 触发器和绑定

HTTP 触发器在函数.jon 文件中定义。 绑定的 `name` 必须与函数中的命名参数匹配。
前面的示例中使用了绑定名称 `req`。 此参数是 [HttpRequest] 对象，并返回 [HttpResponse] 对象。

从 [HttpRequest] 对象中，可以获取请求标头、查询参数、路由参数和消息正文。

以下示例来自[适用于 Python 的 HTTP 触发器模版](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)。

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

在此函数中，`name` 查询参数的值从 [HttpRequest] 对象的 `params` 参数获取。 JSON 编码的消息正文使用 `get_json` 方法来读取。

同样，你可以在返回的 [HttpResponse] 对象中为响应消息设置 `status_code` 和 `headers`。

## <a name="scaling-and-concurrency"></a>缩放和并发

默认情况下，Azure Functions 会自动监视应用程序的负载，并根据需要为 Python 创建其他主机实例。 Functions 针对不同触发器类型使用内置（用户不可配置）阈值来确定何时添加实例，例如 QueueTrigger 的消息和队列大小。 有关详细信息，请参阅[消耗计划和高级计划的工作原理](functions-scale.md#how-the-consumption-and-premium-plans-work)。

此缩放行为对许多应用程序都已经足够。 但是，具有以下任何特征的应用程序可能无法有效进行缩放：

- 应用程序需要处理许多并发调用。
- 应用程序处理大量 I/O 事件。
- 应用程序受 I/O 约束。

在此类情况下，可以通过采用异步模式和使用多个语言工作进程来进一步提高性能。

### <a name="async"></a>异步

由于 Python 是单线程运行时，因此 Python 的主机实例一次只能处理一次函数调用。 对于处理大量 I/O 事件和/或受 I/O 约束的应用程序，可以通过异步运行函数来提高性能。

若要以异步方式运行函数，请使用 `async def` 语句，该语句直接使用 [asyncio](https://docs.python.org/3/library/asyncio.html) 运行该函数：

```python
async def main():
    await some_nonblocking_socket_io_op()
```

没有 `async` 关键字的函数在 asyncio 线程池中自动运行：

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>使用多个语言工作进程

默认情况下，每个函数主机实例都有一个语言工作进程。 利用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 应用程序设置，你可以增加每个主机的工作进程数（多达 10 个）。 然后，Azure Functions 尝试在这些辅助角色之间均匀分配同时发出的函数调用。

FUNCTIONS_WORKER_PROCESS_COUNT 适用于 Functions 在横向扩展应用程序来满足需求时创建的每一个主机。

## <a name="context"></a>上下文

若要在执行过程中获取函数的调用上下文，请在其签名中包含 [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) 参数。

例如：

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[Context](/python/api/azure-functions/azure.functions.context?view=azure-python) 类具有以下字符串属性：

`function_directory` 在其中运行函数的目录。

`function_name` 函数的名称。

`invocation_id` 当前函数调用的 ID。

## <a name="global-variables"></a>全局变量

应用的状态是否将保留，以供将来执行使用，这一点不能保证。 但是，Azure Functions 运行时通常会为同一应用的多次执行重复使用同一进程。 为缓存成本昂贵的计算结果，请将其声明为全局变量。

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>环境变量

在 Functions 中，服务连接字符串等[应用程序设置](functions-app-settings.md)在执行过程中将公开为环境变量。 可以通过声明 `import os` 并使用 `setting = os.environ["setting-name"]` 来访问这些设置。

以下示例获取了[应用程序设置](functions-how-to-use-azure-function-app-settings.md#settings)，其中键名为 `myAppSetting`：

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

对于本地开发，应用程序设置[在 local.settings.json 文件中维护](functions-run-local.md#local-settings-file)。

## <a name="python-version"></a>Python 版本

Azure Functions 支持以下 Python 版本：

| Functions 版本 | Python<sup>*</sup> 版本 |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>正式 CPython 分发

若要在 Azure 中创建函数应用时请求特定的 Python 版本，请使用 [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) 命令的 `--runtime-version` 选项。 函数运行时版本由 `--functions-version` 选项设置。 Python 版本在创建函数应用时设置，并且无法更改。

在本地运行时，运行时使用可用的 Python 版本。

## <a name="package-management"></a>包管理

在使用 Azure Functions Core Tools 或 Visual Studio Code 进行本地开发时，将所需包的名称和版本添加到 `requirements.txt` 文件并使用 `pip` 安装它们。

例如，可以使用以下要求文件和 pip 命令从 PyPI 安装 `requests` 包。

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>发布到 Azure

准备好进行发布时，确保所有公开发布的依赖项都在 requirements.txt 文件（位于项目目录的根目录）中列出。

发布中排除的项目文件和文件夹（包括虚拟环境文件夹）列在 .funcignore 文件中。

将 Python 项目发布到 Azure 时，支持三种生成操作：

+ 远程生成：依赖项根据 requirements.txt 文件的内容远程获取。 [远程生成](functions-deployment-technologies.md#remote-build)是推荐的生成方法。 远程也是 Azure 工具的默认生成选项。
+ 本地生成：依赖项根据 requirements.txt 文件的内容在本地获取。
+ 自定义依赖项：你的项目使用未向我们的工具公开发布的软件包。 （需要 Docker。）

若要生成依赖项并使用持续交付 (CD) 系统发布，请[使用 Azure Pipelines](functions-how-to-azure-devops.md)。

### <a name="remote-build"></a>远程生成

默认情况下，使用下面的 [func azure functionapp publish](functions-run-local.md#publish) 命令将 Python 项目发布到 Azure 时，Azure Functions Core Tools 会请求远程生成。

```bash
func azure functionapp publish <APP_NAME>
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

默认情况下，[适用于 Visual Studio Code 的 Azure Functions 扩展](functions-create-first-function-vs-code.md#publish-the-project-to-azure)还会请求远程生成。

### <a name="local-build"></a>本地生成

可以使用下面的 [func azure functionapp publish](functions-run-local.md#publish) 命令发布本地生成，从而防止执行远程生成。

```command
func azure functionapp publish <APP_NAME> --build local
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

使用 `--build local` 选项，从 requirements.txt 文件中读取项目依赖项，同时在本地下载并安装这些依赖包。 项目文件和依赖项从本地计算机部署到 Azure。 这会将较大的部署包上传到 Azure。 如果由于某种原因，Core Tools 无法获取 requirements.txt 文件中的依赖项，则必须使用自定义依赖项选项进行发布。

### <a name="custom-dependencies"></a>自定义依赖项

如果你的项目使用未向我们的工具公开发布的包，则可以通过将包放在 \_\_app\_\_/.python_packages 目录中，使其可供应用使用。 发布之前，运行以下命令以在本地安装依赖项：

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

使用自定义依赖项时，应使用 `--no-build` 发布选项，因为你已安装依赖项。

```command
func azure functionapp publish <APP_NAME> --no-build
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

## <a name="unit-testing"></a>单元测试

可以使用标准测试框架像测试其他 Python 代码那样测试使用 Python 编写的函数。 对于大多数绑定，可以通过从 `azure.functions` 包创建适当类的实例来创建 mock 输入对象。 由于 [`azure.functions`](https://pypi.org/project/azure-functions/) 包不可供立即可用，请务必通过 `requirements.txt` 文件安装该包，如上文[包管理](#package-management)部分所述。

例如，以下是 HTTP 触发函数的 mock 测试：

```json
{
  "scriptFile": "__init__.py",
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
# __app__/HttpTrigger/__init__.py
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
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

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

下面是另一个示例，其中含有队列触发函数：

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

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
## <a name="temporary-files"></a>临时文件

`tempfile.gettempdir()` 方法返回一个临时文件夹，这在 Linux 上为 `/tmp`。 应用程序可使用此目录存储函数在执行期间生成和使用的临时文件。

> [!IMPORTANT]
> 不能保证写入临时目录的文件会在调用之间保留。 在横向扩展期间，临时文件不会在实例之间进行共享。

以下示例在临时目录 (`/tmp`) 中创建一个命名的临时文件：

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

建议你在独立于项目文件夹的文件夹中维护测试。 这样可防止对应用部署测试代码。

## <a name="cross-origin-resource-sharing"></a>跨域资源共享

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Python 函数应用完全支持 CORS。

## <a name="known-issues-and-faq"></a>已知问题和常见问题解答

根据你提供的宝贵反馈，我们才能够维护常见问题故障排除指南的列表：

* [ModuleNotFoundError 和 ImportError](recover-module-not-found.md)

所有已知问题和功能请求都使用 [GitHub 问题](https://github.com/Azure/azure-functions-python-worker/issues)列表进行跟踪。 如果遇到 GitHub 中未列出的问题，请打开“新问题”并提供问题的详细说明。

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
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
