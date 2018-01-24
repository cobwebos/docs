---
title: "在本地开发并运行 Azure Functions | Microsoft Docs"
description: "了解如何在本地计算机上对 Azure 函数进行编码和测试，然后在 Azure Functions 中运行。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 081da55bb956db2879d7f2c5c91d25f5c36d5507
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="code-and-test-azure-functions-locally"></a>在本地对 Azure Functions 进行编码和测试

虽然 [Azure 门户]提供了一整套用于开发和测试 Azure Functions 的工具，但许多开发人员更喜欢本地开发体验。 Azure Functions 方便用户使用其喜欢的代码编辑器和本地开发工具在本地计算机上开发和测试函数。 其函数可在 Azure 中触发事件，并且用户可以在本地计算机上调试 C# 和 JavaScript 函数。 

如果是 Visual Studio C# 开发人员，Azure Functions 还可以[与 Visual Studio 2017 集成](functions-develop-vs.md)。

>[!IMPORTANT]  
> 不要将本地开发和门户开发混合在同一函数应用中。 从本地项目创建和发布函数时，不应尝试维护或修改门户中的项目代码。

## <a name="install-the-azure-functions-core-tools"></a>安装 Azure Functions Core Tools

[Azure Functions Core Tools] 是 Azure Functions 运行时的本地版本，可在本地开发计算机上运行。 它既不是仿真器，也不是模拟器。 它与在 Azure 中运行的 Functions 运行时相同。 Azure Functions Core Tools 有两个版本，一个适用于运行时版 1.x，另一个适用于版本 2.x。 这两个版本都以 [npm 包](https://docs.npmjs.com/getting-started/what-is-npm)的形式提供。

>[!NOTE]  
> 在安装任一版本之前，必须[安装包含 npm 的 NodeJS](https://docs.npmjs.com/getting-started/installing-node)。 对于 2.x 版工具，仅支持 Node.js 8.5 和更高版本。 

### <a name="version-1x-runtime"></a>1.x 版运行时

工具的原始版本使用 Functions 1.x 运行时。 此版本使用 .NET Framework，仅在 Windows 计算机上受支持。 使用以下命令安装 1.x 版工具：

```bash
npm install -g azure-functions-core-tools
```

### <a name="version-2x-runtime"></a>2.x 版运行时

2.x 版工具使用构建在 .NET Core 之上的 Azure Functions 运行时 2.x。 .NET Core 2.x 支持的所有平台都支持此版本。 在进行跨平台开发以及需要 Functions 运行时 2.x 时，可以使用此版本。 

>[!IMPORTANT]   
> 在安装 Azure Functions Core Tools 之前，请[安装 .NET Core 2.0](https://www.microsoft.com/net/core)。  
>
> Azure Functions 运行时 2.0 处于预览版阶段，目前 Azure Functions 的全部功能并非都可受到支持。 有关详细信息，请参阅 [Azure Functions 运行时 2.0 已知问题](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)。 

 使用以下命令安装 2.0 版工具：

```bash
npm install -g azure-functions-core-tools@core
```

在 Ubuntu 上安装时，请使用 `sudo`，如下所示：

```bash
sudo npm install -g azure-functions-core-tools@core
```

在 macOS 和 Linux 上安装时，可能需要包含 `unsafe-perm` 标志，如下所示：

```bash
sudo npm install -g azure-functions-core-tools@core --unsafe-perm true
```

## <a name="run-azure-functions-core-tools"></a>运行 Azure Functions Core Tools
 
Azure Functions Core Tools 添加了以下命令别名：
* func
* azfun
* azurefunctions

在示例中显示的 `func` 位置，可以使用其中的任何别名。

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>创建本地 Functions 项目

在本地运行时，Functions 项目是包含 [host.json](functions-host-json.md) 和 [local.settings.json](#local-settings-file) 的目录。 此目录相当于 Azure 中的一个函数应用。 若要深入了解 Azure Functions 文件夹结构，请参阅 [Azure Functions 开发人员指南](functions-reference.md#folder-structure)。

在终端窗口中或者在命令提示符下，运行以下命令创建项目和本地 Git 存储库：

```
func init MyFunctionProj
```

输出如以下示例所示：

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

若要创建不包含本地 Git 存储库的项目，请使用 `--no-source-control [-n]` 选项。

## <a name="local-settings-file"></a>本地设置文件

文件 local.settings.json 存储 Azure Functions Core Tools 的应用设置、连接字符串和设置。 其结构如下：

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| 设置      | 说明                            |
| ------------ | -------------------------------------- |
| IsEncrypted | 设置为“true”时，使用本地计算机密钥加密所有值。 与 `func settings` 命令配合使用。 默认值为“false”。 |
| **值** | 在本地运行时所使用的一系列应用程序设置。 **AzureWebJobsStorage** 和 **AzureWebJobsDashboard** 为示例；有关完整列表，请参阅[应用设置参考](functions-app-settings.md)。  |
| **主机** | 在本地运行时，本部分中的设置会自定义 Functions 主机进程。 | 
| LocalHttpPort | 设置运行本地 Functions 主机时使用的默认端口（`func host start` 和 `func run`）。 `--port` 命令行选项优先于此值。 |
| **CORS** | 定义[跨域资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)可以使用的来源。 以逗号分隔的列表提供来源，其中不含空格。 支持通配符值 (\*)，它允许使用任何来源的请求。 |
| ConnectionStrings | 包含函数的数据库连接字符串。 此对象中的连接字符串添加到提供者类型为 System.Data.SqlClient 的环境中。  | 

大多数触发器和绑定都有 Connection 属性，该属性映射到环境变量或应用设置的名称。 对于每个连接属性，必须具有在 local.settings.json 文件中定义的应用设置。 

代码中还可以将这些设置读取为环境变量。 在 C# 中，使用 [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) 或 [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx)。 在 JavaScript 中，使用 `process.env`。 指定为系统环境变量的设置优先于 local.settings.json 文件中的值。 

只有在本地运行时，Functions工具才使用 local.settings.json 文件中的设置。 默认情况下，将项目发布到 Azure 时，这些设置不会自动迁移。 [发布时](#publish)使用 `--publish-local-settings` 开关确保已将这些设置添加到 Azure 中的函数应用。

如果没有为 **AzureWebJobsStorage** 设置有效的存储连接字符串，则会显示以下错误消息：  

>local.settings.json 中的 AzureWebJobsStorage 缺少值。 该值对除 HTTP 以外的所有触发器都是必需的。 可运行“func azure functionapp fetch-app-settings <functionAppName>”或在 local.settings.json 中指定连接字符串。
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>配置应用设置

若要设置连接字符串的值，可执行以下选项之一：
* 通过 [Azure 存储资源管理器](http://storageexplorer.com/)输入连接字符串。
* 使用以下命令之一：

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    这两个命令都要求首先登录到 Azure。

<a name="create-func"></a>
## <a name="create-a-function"></a>创建函数

若要创建函数，请运行以下命令：

```
func new
``` 
`func new` 支持以下可选参数：

| 参数     | 说明                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | C#、F# 或 JavaScript 等模板编程语言。 |
| **`--template -t`** | 模板名称。 |
| **`--name -n`** | 函数名称。 |

例如，若要创建 JavaScript HTTP 触发器，运行：

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

若要创建由队列触发的函数，运行：

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>在本地运行函数

若要运行 Functions 项目，请运行 Functions 主机。 主机为项目中的所有函数启用触发器：

```
func host start
```

`func host start` 支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | 要侦听的本地端口。 默认值：7071。 |
| **`--debug <type>`** | 选项为 `VSCode` 和 `VS`。 |
| **`--cors`** | 以逗号分隔的 CORS 来源列表，其中不包含空格。 |
| **`--nodeDebugPort -n`** | 节点调试程序要使用的端口。 默认值：launch.json 中的值或 5858。 |
| **`--debugLevel -d`** | 控制台跟踪级别（关闭、详情、信息、警告或错误）。 默认：信息。|
| **`--timeout -t`** | Functions 主机启动的超时时间（以秒为单位）。 默认值：20 秒。|
| **`--useHttps`** | 绑定到 https://localhost:{port}，而非 http://localhost:{port}。 默认情况下，此选项会在计算机上创建可信证书。|
| **`--pause-on-error`** | 退出进程前，暂停增加其他输入。 十分适用于从集成开发环境 (IDE) 启动 Azure Functions Core Tools 的情况。|

Functions 主机启动时，会输出 HTTP 触发的函数的 URL：

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>在 VS Code 或 Visual Studio 中进行调试

若要附加调试程序，请传递 `--debug` 参数。 若要调试 JavaScript 函数，请使用 Visual Studio Code。 对于 C# 函数，请使用 Visual Studio。

若要调试 C# 函数，请使用 `--debug vs`。 还可使用 [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/)。 

若要启动主机并设置 JavaScript 调试，运行：

```
func host start --debug vscode
```

然后，在 Visual Studio Code 中的“调试”视图中，选择“附加到 Azure Functions”。 可附加断点、检查变量和逐步执行代码。

![使用 Visual Studio Code 调试 JavaScript](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>将测试数据传递给函数

若要在本地测试函数，请[启动 Functions 主机](#start)，并在本地服务器上使用 HTTP 请求调用终结点。 你调用的终结点要取决于函数的类型。 

>[!NOTE]  
> 本主题中的示例使用 cURL 工具从终端或命令提示符发送 HTTP 请求。 你可以使用所选的工具将 HTTP 请求发送到本地服务器。 默认情况下，在基于 Linux 的系统上提供 cURL 工具。 在 Windows 上，必须先下载并安装 [cURL 工具](https://curl.haxx.se/)。

有关测试函数的更多常规信息，请参阅[在 Azure Functions 中测试代码的策略](functions-test-a-function.md)。

#### <a name="http-and-webhook-triggered-functions"></a>HTTP 和 webhook 触发的函数

调用以下终结点，以在本地运行 HTTP 和 webhook 触发的函数：

    http://localhost:{port}/api/{function_name}

请确保使用相同的服务器名称和 Functions 主机正在侦听的端口。 在启动 Function 主机时所生成的输出中可以看到该信息。 可以使用触发器所支持的任何 HTTP 方法来调用此 URL。 

以下 cURL 命令使用查询字符串中传递的 name 参数从 GET 请求触发 `MyHttpTrigger` quickstart 函数。 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
下面的示例是在请求主体中传递 name 的 POST 请求中调用的相同函数：

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

请注意，可以从在查询字符串中传递数据的浏览器发出 GET 请求。 对于所有其他 HTTP 方法，必须使用 cURL、Fiddler、Postman 或类似的 HTTP 测试工具。  

#### <a name="non-http-triggered-functions"></a>非 HTTP 触发的函数
对于 HTTP 触发器和 webhook 以外的所有类型函数，你可以通过调用管理终结点在本地测试函数。 在本地服务器上通过 HTTP POST 请求调用此终结点会触发该函数。 可以选择通过 POST 请求正文将测试数据传递给执行。 此功能类似于 Azure 门户中的“测试”选项卡。  

可以调用以下管理员终结点以触发非 HTTP 函数：

    http://localhost:{port}/admin/functions/{function_name}

若要将测试数据传递给函数的管理员终结点，必须在 POST 请求消息的正文中提供数据。 消息正文需要具有以下 JSON 格式：

```JSON
{
    "input": "<trigger_input>"
}
```` 
`<trigger_input>` 值包含函数所需格式的数据。 下面的 cURL 示例是指向 `QueueTriggerJS` 函数的 POST。 在这种情况下，输入是一个字符串，等同于期望在队列中找到的消息。      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>在版本 1.x 中使用 `func run` 命令

>[!IMPORTANT]  
> 该工具的 2.x 版本不支持 `func run` 命令。 有关详细信息，请参阅主题[如何指向 Azure Functions 运行时版本](functions-versions.md)。

也可以使用 `func run <FunctionName>` 直接调用函数并为函数提供输入数据。 此命令类似于在 Azure 门户中使用“测试”选项卡运行函数。 

`func run` 支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | 内联内容。 |
| **`--debug -d`** | 运行函数前，将调试程序附加到主机进程。|
| **`--timeout -t`** | 本地 Functions 主机准备就绪前的等待时间（以秒为单位）。|
| **`--file -f`** | 要用作内容的文件名。|
| **`--no-interactive`** | 不提示输入。 适用于自动化方案。|

例如，若要调用 HTTP 触发的函数并传递内容正文，请运行以下命令：

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>发布到 Azure

若要将 Functions 项目发布到 Azure 中的函数应用，使用 `publish` 命令：

```
func azure functionapp publish <FunctionAppName>
```

可以使用以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  将 local.settings.json 中的设置发布到 Azure，如果该设置已存在，则提示进行覆盖。|
| **`--overwrite-settings -y`** | 必须与 `-i` 一起使用。 如果不同，则使用本地值覆盖 Azure 中的 AppSettings。 默认为提示。|

此命令发布到 Azure 中的现有函数应用。 如果订阅中不存在 `<FunctionAppName>`，会发生错误。 若要了解如何使用 Azure CLI 从命令提示符或终端窗口创建函数应用，请参阅[为无服务器执行创建函数应用](./scripts/functions-cli-create-serverless.md)。

`publish` 命令上传 Functions 项目目录的内容。 如果在本地删除文件，`publish` 命令不会将文件从 Azure 中删除。 可以使用 [Azure 门户]中的 [Kudu 工具](functions-how-to-use-azure-function-app-settings.md#kudu)删除 Azure 中的文件。  

>[!IMPORTANT]  
> 在 Azure 中创建函数应用时，该应用默认使用 1.x 版函数运行时。 若要让函数应用使用 2.x 版运行时，请添加应用程序设置 `FUNCTIONS_EXTENSION_VERSION=beta`。  
使用以下 Azure CLI 代码将此设置添加到函数应用： 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>后续步骤

Azure Functions Core Tools 是[开源工具且托管在 GitHub 上](https://github.com/azure/azure-functions-cli)。  
若要提交 bug 或功能请求，[请打开 GitHub 问题](https://github.com/azure/azure-functions-cli/issues)。 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 门户]: https://portal.azure.com 
