---
title: 通过 Visual Studio Code 在 Python 中创建和部署 Azure Functions
description: 如何使用 Azure Functions 的 Visual Studio Code 扩展在 Python 中创建无服务器函数并将其部署到 Azure。
services: functions
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: 590757f78086be894cdc2384bb4a4df380e91c27
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098606"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>通过 Visual Studio Code 将 Python 部署到 Azure Functions

在本教程中，你将使用 Visual Studio Code 和 Azure Functions 扩展创建具有 Python 的无服务器 HTTP 终结点，还可以将连接（或 "绑定"）添加到存储。 Azure Functions 在无服务器环境中运行你的代码，而无需预配虚拟机或发布 web 应用。 Visual Studio Code 的 Azure Functions 扩展极大地简化了通过自动处理许多配置注意事项来使用函数的过程。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 安装 Azure Functions 扩展
> * 创建 HTTP 触发的函数
> * 本地调试
> * 同步应用程序设置
> * 查看流式处理日志
> * 连接到 Azure 存储

如果在本教程中的任何步骤中都遇到问题，我们将很乐意听到详细信息。 使用每节末尾的 "**我遇到问题**" 按钮提交详细的反馈。

## <a name="prerequisites"></a>先决条件

- 一个 [Azure 订阅](#azure-subscription)。
- [Visual Studio Code 具有 Azure Functions](#visual-studio-code-python-and-the-azure-functions-extension)扩展。
- [Azure Functions Core Tools](#azure-functions-core-tools)。

### <a name="azure-subscription"></a>Azure 订阅

如果你没有 Azure 订阅，请[立即注册](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)一个免费的30天帐户，其中包含 $200 的 azure 额度，以试用服务的任何组合。

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code、Python 和 Azure Functions 扩展

安装以下软件：

- Azure Functions 所需的 Python 3.6. x。 [Python 3.6.9](https://www.python.org/downloads/release/python-369/)是最新的3.6 版。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Visual Studio Code Python 教程-先决条件](https://code.visualstudio.com/docs/python/python-tutorial)中所述的[Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。
- [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 有关一般信息，请访问[vscode-Azurefunctions GitHub 存储库](https://github.com/Microsoft/vscode-azurefunctions)。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

请按照有关[使用 Azure Functions Core Tools](functions-run-local.md#v2)的操作系统的说明进行操作。 这些工具本身是用 .NET Core 编写的，核心工具包最好使用 node.js 包管理器（npm）进行安装，这就是为什么需要安装 .NET Core 和 node.js，甚至是用于 Python 代码的原因。 但你可以使用 "扩展捆绑" 跳过 .NET Core 要求，如前面提到的文档中所述。 无论是哪种情况，只需安装这些组件一次，之后 Visual Studio Code 会自动提示您安装任何更新。

### <a name="sign-in-to-azure"></a>登录 Azure

安装函数扩展后，请转到**azure 以登录到 azure 帐户：函数**资源管理器中，选择 "**登录到 Azure**"，然后按照提示进行操作。

![通过 Visual Studio Code 登录到 Azure](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

登录后，验证 Azure 订阅的电子邮件帐户是否显示在状态栏中：

![显示 Azure 帐户 Visual Studio Code 状态栏](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

分配给订阅的名称也会显示在**Azure 中：函数**资源管理器（下图中的 "主"）：

![显示订阅的 Visual Studio Code Azure App Service 资源管理器](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> 如果遇到错误 "找**不到名称 [订阅 ID] 的订阅"** ，这可能是因为你在代理后并且无法访问 Azure API。 在`HTTP_PROXY`终端`HTTPS_PROXY`中通过代理信息配置和环境变量：
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>验证先决条件

若要验证是否安装了所有 Azure Functions 工具，请打开 Visual Studio Code 命令面板（F1），选择**终端：创建新的集成**终端命令，打开终端后，运行以下命令： `func`

![检查 Azure Functions 核心工具先决条件](media/tutorial-vs-code-serverless-python/check-prereqs.png)

以 Azure Functions 徽标开头的输出（需要向上滚动输出）指示 Azure Functions Core Tools 存在。

如果此`func`命令无法识别，请验证您安装了 Azure Functions Core Tools 的文件夹是否包含在 PATH 环境变量中。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>创建函数

1. Azure Functions 的代码是在创建代码前先创建的函数_项目_中进行管理的。 在**Azure 中：函数**资源管理器（使用左侧的 Azure 图标打开），选择 "**新建项目**" 命令图标，或打开命令面板（F1），然后选择 **"Azure Functions：** Create New Project”。

    ![函数资源管理器中的 "新建项目" 按钮](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. 在下面的提示中：

    | Prompt | ReplTest1 | 描述 | 
    | --- | --- | --- |
    | 为项目指定文件夹 | 当前打开的文件夹 | 要在其中创建项目的文件夹。 你可能想要在子文件夹中创建项目。 |
    | 选择函数应用项目的语言 | **Python** | 用于函数的语言，该语言确定用于代码的模板。 |
    | 为项目的第一个函数选择模板 | **HTTP 触发器** | 每当向函数的终结点发出 HTTP 请求时，都将运行使用 HTTP 触发器的函数。 （还有其他用于 Azure Functions 的触发器。 若要了解详细信息，请参阅[如何处理函数？](functions-overview.md#what-can-i-do-with-functions)。） |
    | 提供函数名称 | HttpExample | 该名称用于包含函数的代码以及配置数据的子文件夹，并且还定义了 HTTP 终结点的名称。 使用 "HttpExample" 而不是接受默认的 "HTTPTrigger"，以将函数本身与触发器区分开来。 |
    | 身份验证级别 | **Function** | 对函数终结点进行的调用需要[函数键](functions-bindings-http-webhook.md#authorization-keys)。 |
    | 选择打开项目的方式 | **在当前窗口中打开** | 在当前 Visual Studio Code 窗口中打开项目。 |

1. 短时间之后，会出现一条消息，指示已创建新项目。 在**资源管理器**中，为函数创建了子文件夹。 

1. 如果该文件尚未打开，请打开 *\_ \_\_\_* 包含默认函数代码的 py 文件：

    [![创建新的 Python 函数项目的结果](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > 当你打开 *\_ \_py 时，Visual Studio Code 如果你未选择 python 解释器，请打开命令面板（F1），然后选择 python：\_\_* **选择 "** 解释器命令"，然后选择本地`.env`文件夹中的虚拟环境（作为项目的一部分创建）。 环境必须基于 Python 3.6 x，如前面的[先决条件](#prerequisites)中所述。
    >
    > ![选择用项目创建的虚拟环境](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>检查代码文件

在新创建的_HttpExample_ function 子文件夹中为三个文件：  *\_ \_\_\_py*包含函数的代码，*函数。 json*介绍了 Azure 的函数函数和*示例 .dat*是一个示例数据文件。 如果需要，你可以删除*示例 .dat* ，因为它只是为了表明可以将其他文件添加到子文件夹中。

首先查看*函数 json* ，然后查看 *\_py\_ \_中\_* 的代码。

### <a name="functionjson"></a>function.json

函数 json 文件提供 Azure Functions 终结点所需的配置信息：

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

属性标识代码的启动文件，并且该代码必须包含一个名为`main`的 Python 函数。 `scriptFile` 可以将代码分解成多个文件，只要此处指定的文件包含`main`函数。

`bindings`元素包含两个对象，一个用于描述传入的请求，另一个用于描述 HTTP 响应。 对于传入的请求`"direction": "in"`（），该函数会响应 HTTP GET 或 POST 请求，并要求你提供函数键。 响应（`"direction": "out"`）是返回`main`从 Python 函数返回的任何值的 HTTP 响应。

### <a name="__initpy__"></a>\_\_init.py\_\_

创建新函数时，Azure Functions 在 py 中提供默认的 *\_ \_\_\_* Python 代码：

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

代码的重要部分如下所示：

- 你必须导`func`入`azure.functions`，但导入日志记录模块是可选的，但建议这样做。
- 所需`main`的 Python 函数`func.request`接收名为`req`的对象，并返回一个类型`func.HttpResponse`的值。 可以在 func 中了解有关这些对象的功能的详细信息[。HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python)和[func。Httpresponse.cache](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python)引用。
- `main`然后处理请求并生成响应。 在这种情况下，代码将在`name` URL 中查找参数。 如果失败，它将检查请求正文是否包含 json （使用`func.HttpRequest.get_json`），并且 JSON 是否`name`包含值（使用`get`由`get_json`返回的 JSON 对象的方法）。
- 如果找到了名称，则代码将返回字符串 "Hello"，并追加名称;否则，它会返回一条错误消息。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>本地调试

1. 创建函数项目时，Visual Studio Code 扩展还会在中创建一个启动配置， `.vscode/launch.json`其中包含名为 "**附加到 Python 函数**" 的单个配置。 此配置意味着只需选择**F5**或使用 "调试资源管理器" 启动项目：

    ![显示函数启动配置的调试浏览器](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. 启动调试器时，会打开，其中显示 Azure Functions 的输出，包括可用终结点的摘要。 如果使用的名称不是 "HttpExample"，则 URL 可能会有所不同：

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. 在 "Visual Studio Code**输出**" 窗口中，使用**Ctrl + 单击**或**Cmd + 单击**URL 打开浏览器并将浏览器粘贴到同一 URL。 `api/<function_name>`在`api/HttpExample`这两种情况下，端点为，在本例中为。 但是，因为该 URL 不包含 name 参数，所以，浏览器窗口应该只显示 "请在查询字符串或请求正文中传递一个名称" 作为代码中该路径的相应路径。

1. 现在尝试添加使用的名称参数（如`http://localhost:7071/api/HttpExample?name=VS%20Code`），浏览器窗口应显示消息 "Hello Visual Studio Code！"，演示已运行该代码路径。

1. 若要在 JSON 请求正文中传递 name 值，可以使用带曲线的工具，并将其作为内联：

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    或者，创建一个包含`{"name":"Visual Studio Code"}`和使用命令`curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`的文件，如*数据。*

1. 若要调试函数，请在读取`name = req.params.get('name')`并再次向 URL 发出请求的行上设置一个断点。 Visual Studio Code 调试器应该在该行停止，使您能够检查变量并单步执行代码。 （有关基本调试的简短演练，请参阅[Visual Studio Code 教程-配置和运行调试器](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger)。）

1. 如果你认为已在本地全面测试函数，请停止调试器（通过 "**调试** > " "**停止调试**" 菜单命令或在 "调试" 工具栏上的 "**断开连接**" 命令）。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>部署到 Azure Functions

在这些步骤中，将使用函数扩展在 Azure 中创建函数应用，以及其他所需的 Azure 资源。 函数应用可将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 它还需要用于数据和[托管计划](functions-scale.md#hosting-plan-support)的 Azure 存储帐户。 所有这些资源都组织在单个资源组中。

1. 在“Azure：函数” **"** 函数资源管理器" 中，选择 "**部署到 Function App** " 命令，或打开命令面板（ **F1），选择 Azure Functions：部署到 Function App**命令。 同样，函数应用是你的 Python 项目在 Azure 中运行的位置。

    ![部署到 Function App 命令](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. 出现提示时，选择 "**在 azure 中创建新的 Function App**，并提供一个在 azure 中唯一的名称（通常使用个人名称或公司名称以及其他唯一标识符; 可以使用字母、数字和连字符）。 如果以前创建了 Function App，则其名称将显示在此选项列表中。

1. 此扩展执行以下操作，你可以在 Visual Studio Code 弹出消息和 "**输出**" 窗口中观察此操作（此过程需要几分钟时间）：

    - 使用您提供的名称创建一个资源组（删除连字符）。
    - 在该资源组中，创建存储帐户、托管计划和函数应用。 默认情况下，将创建[消耗计划](functions-scale.md#consumption-plan)。 若要在专用计划中运行函数，需要[使用高级创建选项启用发布](functions-develop-vs-code.md)。
    - 将代码部署到 function app。

    **Azure：函数**资源管理器还显示进度：

    ![Azure 中的部署进度指示器：函数资源管理器](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. 部署完成后，Azure Functions 扩展会显示一条消息，其中包含三个其他操作的按钮：

    ![指示成功部署并带有其他操作的消息](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    有关**流日志**和**上载设置**，请参阅下一节。 如要**查看输出**，请参阅后面的步骤5。

1. 部署后，"**输出**" 窗口还显示 Azure 上的公共终结点：

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    使用此终结点可以在请求正文中使用 URL 参数和/或对 JSON 数据的请求来运行您在本地执行的相同测试。 公共终结点的结果应与前面测试的本地终结点的结果相匹配。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>流式传输日志

目前正在开发对日志流式处理的支持，如 Azure Functions 扩展的[问题 589](https://github.com/microsoft/vscode-azurefunctions/issues/589)中所述。 部署消息中的 "**流日志**" 按钮将最终将 Azure 上的日志输出连接到 Visual Studio Code。 你还可以通过右键单击 "函数" 项目并选择 "**启动流式处理日志**" 或 "**停止流式处理日志**"，在**Azure Functions**资源管理器上启动和停止日志流。

但目前这些命令尚未运行。 通过运行以下命令，并将替换`<app_name>`为 Azure 上的函数应用名称，可在浏览器中使用日志流式处理：

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>将本地设置同步到 Azure

部署消息弹出窗口中的 "**上传设置**" 按钮会将对*本地. json*文件所做的任何更改应用到 Azure。 还可以通过展开 "函数" 项目节点，右键单击 "**应用程序设置**"，然后选择 "**上传本地设置 ...** "，在 " **Azure Functions**资源管理器" 中调用该命令。你还可以使用命令面板选择**Azure Functions：上传本地**设置命令。

上载设置将更新任何现有设置，并添加在*本地. settings*中定义的任何新设置。 上载不会从 Azure 中删除本地文件中未列出的任何设置。 要删除这些设置，请在 " **Azure Functions**资源管理器" 中展开 "**应用程序设置**" 节点，右键单击设置，然后选择 "**删除设置 ...** "。你还可以直接在 Azure 门户上编辑设置。

若要将通过门户或**Azure 资源管理器**所做的任何更改应用到*web.config*文件中，请右键单击 "**应用程序设置**" 节点，然后选择 "**下载远程设置 ...** " 命令。 你还可以使用命令面板选择**Azure Functions：** 下载远程设置”命令将其下载到 local.settings.json 文件中。

## <a name="add-a-second-function"></a>添加另一个函数

第一次部署后，可以对代码进行更改，如添加其他功能，并将其重新部署到相同的函数应用。

1. 在“Azure：函数” **"** 函数资源管理器" 中，选择 " ****创建函数**" 命令或使用 Azure Functions：从命令**面板创建函数。 为该函数指定以下详细信息：

    - 模板：HTTP 触发器
    - 姓名："DigitsOfPi"
    - 授权级别：匿名

1. 在 Visual Studio Code 文件资源管理器是包含函数名称的子文件夹，该子文件夹又包含名为 *\_ \_\_\_py*、*函数 json*和*示例*的文件。

1. 替换 py 的内容 *\_ \_以匹配以下\_代码，这将生成一个字符串，该字符串包含 PI 的值与 url 中指定的位数（此代码仅使用 url 参数）\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. 因为该代码仅支持 HTTP GET，请修改*函数 json* ，以便`"methods"`仅`"get"`包含（即删除`"post"`）的集合。 整个文件应如下所示：

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
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

1. 通过选择 " **F5** " 或选择 "**调试** > " "**开始调试**" 菜单命令来启动调试器。 "**输出**" 窗口现在应显示项目中的两个终结点：

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. 在浏览器中或从曲线发出请求`http://localhost:7071/api/DigitsOfPi?digits=125` ，并观察输出。 （你可能会注意到，代码算法并不完全准确，但我们会将你的改进留给你！）完成后，停止调试器。

1. 使用**Azure 中的 "**部署到 Function App** " 重新部署代码：函数**资源管理器。 如果系统提示，请选择之前创建的 Function App。

1. 部署完成后（需要几分钟时间！），"**输出**" 窗口将显示可用于重复测试的公共终结点。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>添加绑定以将消息写入 Azure 存储

_绑定_使你可以将函数代码连接到 Azure 存储等资源，而无需编写任何数据访问代码。 绑定在*函数 json*文件中定义，并且可以同时表示输入和输出。 函数可以使用多个输入和输出绑定，但只能使用一个触发器。 有关详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

在本部分中，会将一个存储绑定添加到本教程前面部分创建的 HttpExample 函数。 函数使用此绑定将消息写入每个请求的存储中。 相关存储使用的是函数应用使用的默认存储帐户。 但是，如果您计划对存储进行大量使用，则需要考虑创建一个单独的帐户。

1. 通过打开命令面板并选择**Azure Functions，将 Azure Functions 项目的远程设置同步到你的*本地. json*文件中：下载远程设置**。 打开 "*本地. json* " 并检查它是否`AzureWebJobsStorage`包含的值。 该值是存储帐户的连接字符串。

1. 在文件夹中，右键单击*函数 json*，选择 "**添加绑定**"： `HttpExample`

    ![在 Visual Studio Code 资源管理器中添加绑定命令](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. 在 Visual Studio Code 中跟随的提示中，选择或提供以下值：

    | Prompt | 要提供的值 |
    | --- | --- |
    | 设置绑定方向 | out |
    | 选择向外定向绑定 | Azure 队列存储 |
    | 用于在代码中标识此绑定的名称 | 缺少 |
    | 消息将发送到的队列 | outqueue |
    | 从 "*设置*" 中选择 "设置" （请求存储连接） | AzureWebJobsStorage |

1. 进行这些选择后，验证是否已将以下绑定添加到*函数 json*文件中：

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. 将*host*的内容替换为以下内容，并添加[扩展绑定引用](functions-bindings-register.md#extension-bundles)。

    ```json
    {
        "version": "2.0",
        "extensionBundle": {
            "id": "Microsoft.Azure.Functions.ExtensionBundle",
            "version": "[1.*, 2.0.0)"
        }
    }
    ```

1. 配置绑定后，可以在函数代码中使用它。 同样，新定义的绑定在代码中`main`显示为 *\_py \_\_\_* 中函数的自变量。 例如，可以在 HttpExample 中修改 *\_ \_\_\_py*文件，使其与以下文件匹配，其中显示了使用`msg`参数编写带有需要. 注释说明了具体更改：

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
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
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. 若要在本地测试这些更改，请通过选择 " **F5** " 或选择 "**调试** > " "**开始调试**" 菜单命令，在 Visual Studio Code 中再次启动调试器。 与 "**输出**" 窗口之前一样，应显示项目中的终结点。

1. 在浏览器中，访问 URL `http://localhost:7071/api/HttpExample?name=VS%20Code`以创建对 HttpExample 终结点的请求，该终结点还应将消息写入队列。

1. 若要验证是否已将消息写入 "outqueue" 队列（在绑定中指定），可以使用以下三种方法之一：

    1. 登录到[Azure 门户](https://portal.azure.com)，然后访问包含函数项目的资源组。 在该资源组中，找到并打开该项目的存储帐户，然后中转到 "**队列**"。 在该页上，请参阅 "outqueue"，它应显示所有记录的消息。

    1. 使用与 Visual Studio 集成的 Azure 存储资源管理器打开并检查队列，如[使用 Visual Studio Code 将函数连接到 Azure 存储](functions-add-output-binding-storage-queue-vs-code.md)中所述，尤其是[检查输出队列](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue)部分。

    1. 使用 Azure CLI 查询存储队列，如[查询存储队列](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue)中所述。
    
1. 若要在云中进行测试，请使用**Azure 中的 "**部署到 Function App** " 重新部署代码：函数**资源管理器。 如果系统提示，请选择之前创建的 Function App。 部署完成后（需要几分钟时间！），"**输出**" 窗口将再次显示可用于重复测试的公共终结点。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>清理资源

你创建的 Function App 包括可能会产生最小成本（请参阅[函数定价](https://azure.microsoft.com/pricing/details/functions/)）的资源。 若要清理资源，请在**Azure 中右键单击 Function App：函数**资源管理器，选择**删除 Function App**。 你还可以访问[Azure 门户](https://portal.azure.com)，从左侧导航窗格中选择 "**资源组**"，选择在本教程的过程中创建的资源组，然后使用 "**删除资源组**" 命令。

## <a name="next-steps"></a>后续步骤

恭喜你完成本演练，将 Python 代码部署到 Azure Functions！ 你现在已准备好创建更多的无服务器函数。

如前文所述，可以通过访问其 GitHub 存储库[vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions)了解有关函数扩展的详细信息。 还欢迎使用问题和发布。

阅读[Azure Functions 概述](functions-overview.md)，了解可以使用的不同触发器。

若要详细了解可通过 Python 使用的 Azure 服务（包括数据存储以及 AI 和机器学习服务），请访问[Azure Python 开发人员中心](/azure/python/?view=azure-python)。

还有其他适用于 Visual Studio Code 的 Azure 扩展可能会有所帮助。 只需在 "扩展资源管理器" 中搜索 "Azure"：

![适用于 Visual Studio Code 的 Azure 扩展](media/tutorial-vs-code-serverless-python/azure-extensions.png)

一些常用扩展包括：

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure 应用服务](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Azure CLI 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
