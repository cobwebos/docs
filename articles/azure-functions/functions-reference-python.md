---
title: Azure Functions Python 开发人员参考
description: 了解如何使用 Pythong 开发函数
ms.topic: article
ms.date: 12/13/2019
ms.custom: devx-track-python
ms.openlocfilehash: 0de25cc804844b5aa414e521fa641761d9a4b4f4
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108416"
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

HTTP 触发器是在文件的 function.js中定义的。 绑定的 `name` 必须与函数中的命名参数匹配。
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

## <a name="scaling-and-performance"></a>缩放和性能

务必要了解函数的执行方式和性能如何影响函数应用的缩放方式。 在设计高性能应用程序时，这一点尤其重要。 下面是在设计、编写和配置函数应用时要考虑的几个因素。

### <a name="horizontal-scaling"></a>水平缩放
默认情况下，Azure Functions 会自动监视应用程序的负载，并根据需要为 Python 创建其他主机实例。 函数为不同的触发器类型使用内置阈值，以决定何时添加实例，如 QueueTrigger 的消息和队列大小的期限。 这些阈值不是用户可配置的。 有关详细信息，请参阅[消耗计划和高级计划的工作原理](functions-scale.md#how-the-consumption-and-premium-plans-work)。

### <a name="improving-throughput-performance"></a>提高吞吐量性能

提高性能的关键是了解应用如何使用资源，并相应地配置 function app。

#### <a name="understanding-your-workload"></a>了解工作负荷

默认配置适用于大多数 Azure Functions 应用程序。 不过，你可以通过基于工作负荷配置文件使用配置来提高应用程序吞吐量的性能。 第一步是了解正在运行的工作负荷的类型。

|| I/o 绑定的工作负荷 | CPU 绑定工作负荷 |
|--| -- | -- |
|函数应用特征| <ul><li>应用需要处理多个并发调用。</li> <li> 应用处理大量 i/o 事件，例如网络调用和磁盘读/写。</li> </ul>| <ul><li>应用执行长时间运行的计算，例如调整图像大小。</li> <li>应用进行数据转换。</li> </ul> |
|示例| <ul><li>Web API</li><ul> | <ul><li>数据处理</li><li> 机器学习推理</li><ul>|

 
> [!NOTE]
>  由于现实世界中的工作负荷通常是 i/o 和 CPU 界限的混合，因此我们建议在实际的生产负载下分析工作负荷。


#### <a name="performance-specific-configurations"></a>特定于性能的配置

了解 function app 的工作负荷配置文件后，可以使用以下配置来提高函数的吞吐量性能。

##### <a name="async"></a>异步

由于 [python 是单线程运行时](https://wiki.python.org/moin/GlobalInterpreterLock)，因此用于 python 的主机实例一次只能处理一个函数调用。 对于处理大量 i/o 事件和/或 i/o 绑定的应用程序，你可以通过异步运行函数来显著提高性能。

若要以异步方式运行函数，请使用 `async def` 语句，该语句直接使用 [asyncio](https://docs.python.org/3/library/asyncio.html) 运行该函数：

```python
async def main():
    await some_nonblocking_socket_io_op()
```
下面是一个使用 HTTP 触发器的函数的示例，该函数使用 [aiohttp](https://pypi.org/project/aiohttp/) http 客户端：

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


没有 `async` 关键字的函数在 asyncio 线程池中自动运行：

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

为了获得异步运行函数的全部好处，你的代码中使用的 i/o 操作/库还需要实现异步。 在定义为异步的函数中使用同步 i/o 操作 **可能会** 影响整体性能。

下面是已实现异步模式的几个客户端库示例：
- [aiohttp](https://pypi.org/project/aiohttp/) -asyncio 的 Http 客户端/服务器 
- [流 API](https://docs.python.org/3/library/asyncio-stream.html) -用于处理网络连接的高级异步/等待就绪基元
- [Janus 队列](https://pypi.org/project/janus/) -线程安全 asyncio 感知队列
- [pyzmq](https://pypi.org/project/pyzmq/) -ZeroMQ 的 Python 绑定
 

##### <a name="use-multiple-language-worker-processes"></a>使用多个语言工作进程

默认情况下，每个函数主机实例都有一个语言工作进程。 使用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 应用程序设置可增加每个主机的工作进程数（最多 10 个）。 然后，Azure Functions 会尝试在这些工作进程之间平均分配同步函数调用。

对于 CPU 绑定的应用，应将语言辅助角色的数量设置为等于或高于每个 function app 可用的内核数。 若要了解详细信息，请参阅 [可用实例 sku](functions-premium-plan.md#available-instance-skus)。 

I/o 绑定的应用程序的数量也可能会增加，因为这会增加超出可用核心数的工作进程数。 请记住，由于所需的上下文切换次数增加，将工作线程数设置得过高可能会影响整体性能。 

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

有三个生成操作支持将 Python 项目发布到 Azure：远程生成、本地生成和使用自定义依赖项的生成。

你还可以使用 Azure Pipelines 来构建依赖项，并使用持续交付 (CD) 发布。 若要了解详细信息，请参阅 [使用 Azure DevOps 持续交付](functions-how-to-azure-devops.md)。

### <a name="remote-build"></a>远程生成

使用远程生成时，在服务器上还原的依赖项和本机依赖项与生产环境匹配。 这会生成较小的部署包。 在 Windows 上开发 Python 应用时使用远程生成。 如果你的项目具有自定义依赖项，则可以 [将远程生成用于额外的索引 URL](#remote-build-with-extra-index-url)。

依赖项是根据 requirements.txt 文件的内容远程获取的。 [远程生成](functions-deployment-technologies.md#remote-build)是推荐的生成方法。 默认情况下，使用下面的 [func azure functionapp publish](functions-run-local.md#publish) 命令将 Python 项目发布到 Azure 时，Azure Functions Core Tools 会请求远程生成。

```bash
func azure functionapp publish <APP_NAME>
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

默认情况下，[适用于 Visual Studio Code 的 Azure Functions 扩展](functions-create-first-function-vs-code.md#publish-the-project-to-azure)还会请求远程生成。

### <a name="local-build"></a>本地生成

依赖项是根据 requirements.txt 文件的内容在本地获取的。 可以使用下面的 [func azure functionapp publish](functions-run-local.md#publish) 命令发布本地生成，从而防止执行远程生成。

```command
func azure functionapp publish <APP_NAME> --build local
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

使用 `--build local` 选项，从 requirements.txt 文件中读取项目依赖项，同时在本地下载并安装这些依赖包。 项目文件和依赖项从本地计算机部署到 Azure。 这会将较大的部署包上传到 Azure。 如果由于某种原因，Core Tools 无法获取 requirements.txt 文件中的依赖项，则必须使用自定义依赖项选项进行发布。

在 Windows 上进行本地开发时，不建议使用本地生成。

### <a name="custom-dependencies"></a>自定义依赖项

如果你的项目在 [Python 包索引](https://pypi.org/)中找不到依赖项，则可以通过两种方式生成项目。 生成方法取决于生成项目的方式。

#### <a name="remote-build-with-extra-index-url"></a>具有额外索引 URL 的远程生成

如果包可从可访问的自定义包索引中获取，请使用远程生成。 在发布之前，请确保 [创建一个](functions-how-to-use-azure-function-app-settings.md#settings) 名为的应用设置 `PIP_EXTRA_INDEX_URL` 。 此设置的值为自定义包索引的 URL。 使用此设置可告诉远程生成 `pip install` 使用 `--extra-index-url` 选项运行。 若要了解详细信息，请参阅 [Python pip 安装文档](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)。

你还可以将基本身份验证凭据用于额外的包索引 Url。 若要了解详细信息，请参阅 Python 文档中的 [基本身份验证凭据](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) 。

#### <a name="install-local-packages"></a>安装本地包

如果你的项目使用未向我们的工具公开发布的包，则可以通过将包放在 \_\_app\_\_/.python_packages 目录中，使其可供应用使用。 发布之前，运行以下命令以在本地安装依赖项：

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

使用自定义依赖项时，应使用 `--no-build` 发布选项，因为已将依赖项安装到项目文件夹中。

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

## <a name="preinstalled-libraries"></a>预安装库

Python 函数运行时有几个库。

### <a name="python-standard-library"></a>Python 标准库

Python 标准库包含每个 Python 分发附带的内置 Python 模块列表。 其中的大多数库可帮助你访问系统功能，如文件 i/o。 在 Windows 系统中，这些库随 Python 一起安装。 在基于 Unix 的系统上，它们由包集合提供。

若要查看这些库的列表的完整详细信息，请访问以下链接：

* [Python 3.6 标准库](https://docs.python.org/3.6/library/)
* [Python 3.7 标准库](https://docs.python.org/3.7/library/)
* [Python 3.8 标准库](https://docs.python.org/3.8/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python 辅助角色依赖项

功能 Python 辅助角色需要一组特定的库。 你还可以在函数中使用这些库，但它们并不是 Python 标准的一部分。 如果函数依赖于其中的任何库，则在 Azure Functions 之外运行时，它们可能无法用于代码。 可以在[setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282)文件中的**安装 \_ 需要**部分找到依赖项的详细列表。

> [!NOTE]
> 如果函数应用的 requirements.txt 包含 `azure-functions-worker` 条目，请将其删除。 函数工作线程由 Azure Functions 平台自动管理，并定期使用新功能和 bug 修复进行更新。 在 requirements.txt 中手动安装旧版本的辅助角色可能会导致意外问题。

### <a name="azure-functions-python-library"></a>Azure Functions Python 库

每个 Python 辅助角色更新包括 [Azure Functions Python 库 (](https://github.com/Azure/azure-functions-python-library)的新版本) 。 由于每个更新都是向后兼容的，因此这种方法可以更轻松地持续更新 Python 函数应用。 可以在 [azure 功能 PyPi](https://pypi.org/project/azure-functions/#history)中找到此库的版本列表。

运行时库版本由 Azure 修复，不能通过 requirements.txt 重写。 `azure-functions`requirements.txt 中的条目仅适用于 linting 和客户认知。

使用以下代码在运行时中跟踪 Python 函数库的实际版本：

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>运行时系统库

有关 Python 辅助角色 Docker 映像中预安装的系统库的列表，请访问以下链接：

|  Functions 运行时  | Debian 版本 | Python 版本 |
|------------|------------|------------|
| 版本 2.x | 拉伸  | [Python 3。6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3。7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| 3\.x 版 | Buster | [Python 3。6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3。7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile) |

## <a name="cross-origin-resource-sharing"></a>跨域资源共享

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Python 函数应用完全支持 CORS。

## <a name="known-issues-and-faq"></a>已知问题和常见问题解答

下面列出了常见问题的疑难解答指南：

* [ModuleNotFoundError 和 ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [无法导入 "cygrpc"](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

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
