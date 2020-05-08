---
title: Azure Functions Python 开发人员参考
description: 了解如何使用 Pythong 开发函数
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: ea128fc7c68b49fc14d796e9a3b91a9dbddd9b26
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780039"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 开发人员指南

本文介绍了如何使用 Python 开发 Azure Functions。 以下内容假定你已阅读 [Azure Functions 开发人员指南](functions-reference.md)。 

有关 Python 中的独立函数示例项目，请参阅[Python 函数示例](/samples/browse/?products=azure-functions&languages=python)。 

## <a name="programming-model"></a>编程模型

Azure Functions 要求函数在 Python 脚本中作为无状态方法来处理输入并生成输出。 默认情况下，运行时期望此方法在 `__init__.py` 文件中作为名为 `main()` 的全局方法实现。 还可以[指定备用入口点](#alternate-entry-point)。

来自触发器和绑定的数据使用在 *function.json* 配置文件中定义的 `name` 属性，通过方法特性绑定到函数。 例如，下面的 _function.json_ 描述一个由名为 `req` 的 HTTP 请求触发的简单函数：

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

根据此定义，包含函数`__init__.py`代码的文件可能类似于以下示例：

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

您可以选择在`scriptFile` *函数 json*文件中指定和`entryPoint`属性，从而更改函数的默认行为。 例如，下面的 _function.json_ 指示运行时使用 _main.py_ 文件中的 `customentry()` 方法作为 Azure 函数的入口点。

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

Python 函数项目的建议文件夹结构类似于以下示例：

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
主\_\_项目文件夹（应用\_\_）可以包含以下文件：

* *local. json*：用于在本地运行时存储应用设置和连接字符串。 此文件不会被发布到 Azure。 若要了解详细信息，请参阅[本地. settings](functions-run-local.md#local-settings-file)。
* *要求 .txt*：包含在发布到 Azure 时系统安装的包的列表。
* *host json*：包含影响函数应用中所有函数的全局配置选项。 此文件会被发布到 Azure。 在本地运行时，并非所有选项都受支持。 若要了解详细信息，请参阅[host。](functions-host-json.md)
* *. funcignore*：（可选）声明不应发布到 Azure 的文件。
* *. .gitignore*：（可选）声明从 git 存储库中排除的文件，如 ""。
* *Dockerfile*：（可选）在[自定义容器](functions-create-function-linux-custom-image.md)中发布项目时使用。

每个函数都有自己的代码文件和绑定配置文件 (function.json)。 

在将项目部署到 Azure 中的函数应用时，主项目（*\_\_应用\_*）文件夹的全部内容应包含在包中，而不是文件夹本身。 我们建议你在与项目文件夹不同的文件夹（在此示例`tests`中）中维护你的测试。 这使你可以在应用中部署测试代码。 有关详细信息，请参阅[单元测试](#unit-testing)。

## <a name="import-behavior"></a>导入行为

您可以使用显式的相对引用和绝对引用来导入函数代码中的模块。 根据上面所示的文件夹结构，以下导入函数 file * \_ \_\_\_app \my\_\_first function\\_\_\_\_py*：

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

以下导入不适*用于*同一文件中：

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

共享代码应保留在* \_ \_应用\_* 中的单独文件夹中。 若要引用*共享\_代码*文件夹中的模块，可以使用以下语法：

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>触发器和输入

在 Azure Functions 中，输入分为两种类别：触发器输入和附加输入。 虽然它们在 `function.json` 文件中并不相同，但它们在 Python 代码中的使用方法却是相同的。  在本地运行时，触发器和输入源的连接字符串或机密应映射到 `local.settings.json` 文件中的值，以及在 Azure 中运行时的应用程序设置。 

例如，以下代码演示了这两个类别之间的差异：

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

调用函数时，HTTP 请求作为 `req` 传递给函数。 将根据路由 URL 中的_ID_从 Azure Blob 存储中检索一个条目，并在函数体`obj`中提供该条目。  此处指定的存储帐户是 AzureWebJobsStorage 应用设置中的连接字符串，该设置与函数应用使用的存储帐户相同。


## <a name="outputs"></a>Outputs

输出可以在返回值和输出参数中进行表示。 如果只有一个输出，则建议使用返回值。 对于多个输出，必须使用输出参数。

若要使用函数的返回值作为输出绑定的值，则绑定的 `name` 属性应在 `function.json` 中设置为 `$return`。

若要生成多个输出， `set()`请使用[`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python)接口提供的方法向绑定分配值。 例如，以下函数可以将消息推送到队列，还可返回 HTTP 响应。

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

## <a name="logging"></a>Logging

通过函数应用中的根[`logging`](https://docs.python.org/3/library/logging.html#module-logging)处理程序可以访问 Azure Functions 运行时记录器。 此记录器绑定到 Application Insights，并允许标记在函数执行期间遇到的警告和错误。

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

若要了解有关日志记录的详细信息，请参阅[Monitor Azure Functions](functions-monitoring.md)。

## <a name="http-trigger-and-bindings"></a>HTTP 触发器和绑定

HTTP 触发器在函数 jon 文件中定义。 `name`绑定的必须与函数中的命名参数匹配。 在前面的示例中，使用了`req`一个绑定名称。 此参数是一个[HttpRequest]对象，并返回一个[httpresponse.cache]对象。

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

在此函数中， `name`查询参数的值是从[HttpRequest]对象的`params`参数获取的。 使用`get_json`方法读取 JSON 编码的消息正文。 

同样，你可以在返回`status_code`的`headers` [httpresponse.cache]对象中为响应消息设置和。

## <a name="scaling-and-concurrency"></a>缩放和并发

默认情况下，Azure Functions 会自动监视应用程序上的负载，并根据需要为 Python 创建更多主机实例。 函数使用不同触发器类型的内置（而非用户可配置）阈值来决定何时添加实例，如 QueueTrigger 的消息和队列大小的期限。 有关详细信息，请参阅[消耗和高级计划的工作](functions-scale.md#how-the-consumption-and-premium-plans-work)原理。

这种缩放行为足以满足许多应用程序的需求。 但是，具有以下任何特征的应用程序可能无法有效地缩放：

- 应用程序需要处理多个并发调用。
- 应用程序处理大量 i/o 事件。
- 应用程序受 i/o 约束。

在这种情况下，你可以通过使用异步模式和使用多个语言辅助进程进一步提高性能。

### <a name="async"></a>Async

由于 Python 是单线程运行时，因此用于 Python 的主机实例一次只能处理一个函数调用。 对于处理大量 i/o 事件和/或 i/o 绑定的应用程序，你可以通过异步运行函数来提高性能。

若要异步运行函数，请使用`async def`语句，该语句将直接使用[asyncio](https://docs.python.org/3/library/asyncio.html)运行函数：

```python
async def main():
    await some_nonblocking_socket_io_op()
```

不带`async`关键字的函数在 asyncio 的线程池中自动运行：

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>使用多个语言辅助进程

默认情况下，每个 Functions 主机实例都有一个语言工作进程。 使用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 应用程序设置可增加每个主机的工作进程数（最多 10 个）。 然后，Azure Functions 会尝试在这些工作进程之间平均分配同步函数调用。 

FUNCTIONS_WORKER_PROCESS_COUNT 适用于 Functions 在横向扩展应用程序以满足需求时创建的每个主机。 

## <a name="context"></a>上下文

若要在执行过程中获取函数的调用上下文，请[`context`](/python/api/azure-functions/azure.functions.context?view=azure-python)在其签名中包含参数。 

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

不保证应用的状态可保留到将来的执行。 不过，Azure Functions 运行时通常会重复使用同一个进程来多次执行同一个应用。 为了缓存高开销计算的结果，请将其声明为全局变量。 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>环境变量

在函数中，[应用程序设置](functions-app-settings.md)（如服务连接字符串）在执行过程中公开为环境变量。 可以通过声明`import os`并使用来访问这些设置`setting = os.environ["setting-name"]`。

以下示例获取[应用程序设置](functions-how-to-use-azure-function-app-settings.md#settings)，其键名为 `myAppSetting`：

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

## <a name="python-version"></a>Python 版本 

Azure Functions 支持以下 Python 版本：

| Functions 版本 | Python<sup>*</sup>版本 |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>官方 CPython 分布

若要在 Azure 中创建函数应用时请求特定的 Python 版本，请使用`--runtime-version` [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create)命令的选项。 函数运行时版本由`--functions-version`选项设置。 Python 版本是在创建函数应用时设置的，无法更改。  

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

准备好发布时，请确保所有公开发布的依赖项都列在 "要求" .txt 文件中，该文件位于项目目录的根目录下。 

从发布中排除的项目文件和文件夹（包括虚拟环境文件夹）将在 funcignore 文件中列出。

有三个生成操作支持将 Python 项目发布到 Azure：

+ 远程生成：根据要求 .txt 文件的内容远程获取依赖关系。 [远程生成](functions-deployment-technologies.md#remote-build)是推荐的生成方法。 远程也是 Azure 工具的默认生成选项。 
+ 本地生成：依赖项要求 .txt 文件的内容在本地获取依赖关系。 
+ 自定义依赖项：项目使用的包不可公开用于我们的工具。 （需要 Docker。）

若要构建依赖项并使用持续交付（CD）系统发布，请[使用 Azure Pipelines](functions-how-to-azure-devops.md)。

### <a name="remote-build"></a>远程生成

默认情况下，当你使用以下[func Azure functionapp publish](functions-run-local.md#publish)命令将 Python 项目发布到 Azure 时，Azure Functions Core Tools 会请求远程生成。 

```bash
func azure functionapp publish <APP_NAME>
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

默认情况下， [Visual Studio Code 的 Azure Functions 扩展](functions-create-first-function-vs-code.md#publish-the-project-to-azure)还请求远程生成。 

### <a name="local-build"></a>本地生成

可以通过使用以下[func azure functionapp publish](functions-run-local.md#publish)命令发布本地生成来阻止执行远程生成。 

```command
func azure functionapp publish <APP_NAME> --build local
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。 

使用`--build local`选项时，将从要求 .txt 文件中读取项目依赖项，并在本地下载和安装这些依赖包。 项目文件和依赖项从本地计算机部署到 Azure。 这会导致将较大的部署包上传到 Azure。 如果由于某种原因，核心工具无法获取要求 .txt 文件中的依赖项，则必须使用自定义依赖项选项进行发布。 

### <a name="custom-dependencies"></a>自定义依赖项

如果你的项目使用的包不可公开用于我们的工具，则可以通过将其放置在\_ \_应用程序\_\_/. python_packages 目录中，使其可用于你的应用程序。 在发布之前，运行以下命令以本地安装依赖项：

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

使用自定义依赖项时，应使用`--no-build`发布选项，因为已安装了依赖项。  

```command
func azure functionapp publish <APP_NAME> --no-build
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

## <a name="unit-testing"></a>单元测试

可以使用标准测试框架，像测试其他 Python 代码一样测试以 Python 编写的函数。 对于大多数绑定，可以通过创建 `azure.functions` 包中相应类的实例来创建模拟输入对象。 由于不[`azure.functions`](https://pypi.org/project/azure-functions/)能立即使用包，因此请务必通过`requirements.txt`文件安装，如上文所述。 [package management](#package-management) 

例如，下面是 HTTP 触发的函数的模拟测试：

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

下面是使用队列触发的函数的另一个示例：

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
## <a name="temporary-files"></a>“临时文件”

`tempfile.gettempdir()`方法返回一个临时文件夹，在 Linux 上是`/tmp`。 应用程序可以使用此目录来存储在执行期间由函数生成和使用的临时文件。 

> [!IMPORTANT]
> 写入临时目录的文件不能保证在调用中保持不变。 在 scale out 过程中，临时文件不会在实例之间共享。 

下面的示例在临时目录（`/tmp`）中创建命名的临时文件：

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

建议你在独立于项目文件夹的文件夹中维护测试。 这使你可以在应用中部署测试代码。 

## <a name="cross-origin-resource-sharing"></a>跨域资源共享

Azure Functions 支持跨域资源共享（CORS）。 CORS 是[在门户中](functions-how-to-use-azure-function-app-settings.md#cors)和通过[Azure CLI](/cli/azure/functionapp/cors)配置的。 CORS 允许的源列表应用于 function app 级别。 启用 CORS 后，响应包括`Access-Control-Allow-Origin`标头。 有关详细信息，请参阅 [跨域资源共享](functions-how-to-use-azure-function-app-settings.md#cors)。 

Python function apps 完全支持 CORS。

## <a name="known-issues-and-faq"></a>已知问题和常见问题解答

所有已知问题和功能请求都使用 [GitHub 问题](https://github.com/Azure/azure-functions-python-worker/issues)列表进行跟踪。 如果遇到 GitHub 中未列出的问题，请打开“新问题”并提供问题的详细说明。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [Azure Functions 包 API 文档](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions 最佳做法](functions-best-practices.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [Blob 存储绑定](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 绑定](functions-bindings-http-webhook.md)
* [存储绑定](functions-bindings-storage-queue.md)
* [计时器触发器](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
