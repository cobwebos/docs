---
title: "在本地开发并运行 Azure Functions | Microsoft Docs"
description: "了解如何在本地计算机上对 Azure 函数进行编码和测试，然后在 Azure Functions 中运行。"
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 07/12/2017
ms.author: donnam, glenga
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 07ad15c61bd4b3912dfa2f629218deebdebd6dc8
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="code-and-test-azure-functions-locally"></a>在本地对 Azure 函数进行编码和测试

虽然 [Azure 门户]提供了一整套用于开发和测试 Azure Functions 的工具，但许多开发人员更喜欢本地开发体验。 Azure Functions 方便用户使用其喜欢的代码编辑器和本地开发工具在本地计算机上开发和测试函数。 其函数可在 Azure 中触发事件，并且用户可以在本地计算机上调试 C# 和 JavaScript 函数。 

如果是 Visual Studio C# 开发人员，Azure Functions 还可以[与 Visual Studio 2017 集成](functions-develop-vs.md)。

## <a name="install-the-azure-functions-core-tools"></a>安装 Azure Functions Core Tools

Azure Functions Core Tools 是 Azure Functions 运行时的本地版本，可在本地 Windows 计算机上运行。 它既不是仿真器，也不是模拟器。 它与在 Azure 中运行的 Functions 运行时相同。

[Azure Functions Core Tools] 作为 npm 包提供。 必须首先[安装 NodeJS](https://docs.npmjs.com/getting-started/installing-node)，其中包括 npm。  

>[!NOTE]
>目前只能在 Windows 计算机上安装Azure Functions Core Tools 包。 具有此限制的原因在于 Functions 主机中有临时限制。

[Azure Functions Core Tools] 增加以下命令别名：
* func
* azfun
* azurefunctions

可以使用这些别名代替本主题的示例中显示的 `func`。

## <a name="create-a-local-functions-project"></a>创建本地 Functions 项目

在本地运行时，Functions 项目是具有 host.json 和 local.settings.json 的目录。 此目录相当于 Azure 中的一个函数应用。 若要深入了解 Azure Functions 文件夹结构，请参阅 [Azure Functions 开发人员指南](functions-reference.md#folder-structure)。

请在命令提示符处运行以下命令：

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

若决定放弃创建 Git 存储库，请使用 `--no-source-control [-n]` 选项。

<a name="local-settings"></a>

## <a name="local-settings-file"></a>本地设置文件

文件 local.settings.json 存储 Azure Functions Core Tools 的应用设置、连接字符串和设置。 其结构如下：

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>", 
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
| **值** | 在本地运行时所使用的一系列应用程序设置。 将应用程序设置添加到此对象。  |
| AzureWebJobsStorage | 将连接字符串设置为 Azure Functions 运行时在内部使用的 Azure 存储帐户。 存储帐户支持函数的触发器。 除 HTTP 触发的函数以外，所有函数都需要此存储帐户连接设置。  |
| AzureWebJobsDashboard | 将连接字符串设置为用于存储函数日志的 Azure 存储帐户。 此值为可选项，能使日志可在门户中访问。|
| **主机** | 在本地运行时，本部分中的设置会自定义 Functions 主机进程。 | 
| LocalHttpPort | 设置运行本地 Functions 主机时使用的默认端口（`func host start` 和 `func run`）。 `--port` 命令行选项优先于此值。 |
| **CORS** | 定义[跨域资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)可以使用的来源。 以逗号分隔的列表提供来源，其中不含空格。 支持通配符值 (**\***)，它允许使用任何来源的请求。 |
| ConnectionStrings | 包含函数的数据库连接字符串。 此对象中的连接字符串添加到提供者类型为 System.Data.SqlClient 的环境中。  | 

大多数触发器和绑定都有 Connection 属性，该属性映射到环境变量或应用设置的名称。 对于每个连接属性，必须具有在 local.settings.json 文件中定义的应用设置。 

代码中还可以将这些设置读取为环境变量。 在 C# 中，使用 [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) 或 [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx)。 在 JavaScript 中，使用 `process.env`。 指定为系统环境变量的设置优先于 local.settings.json 文件中的值。 

只有在本地运行时，Functions工具才使用 local.settings.json 文件中的设置。 默认情况下，将项目发布到 Azure 时，这些设置不会自动迁移。 [发布时](#publish)使用 `--publish-local-settings` 开关确保已将这些设置添加到 Azure 中的函数应用。

如果没有为 **AzureWebJobsStorage** 设置有效的存储连接字符串，则会显示以下错误消息：  

>local.settings.json 中的 AzureWebJobsStorage 缺少值。 该值对除 HTTP 以外的所有触发器都是必需的。 可运行“func azure functionary fetch-app-settings <functionAppName>”或在 local.settings.json 中指定连接字符串。
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>配置应用设置

若要设置连接字符串的值，可执行以下任一操作：
* 通过 [Azure 存储资源管理器](http://storageexplorer.com/)输入连接字符串。
* 使用以下命令之一：

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure functionapp storage fetch-connection-string <StorageAccountName>
    ```
    这两个命令都要求首先登录到 Azure。

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

也可以使用 `func run <FunctionName>` 直接调用函数并为函数提供输入数据。 此命令类似于在 Azure 门户中使用“测试”选项卡运行函数。 此命令启动整个 Functions 主机。

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

`publish` 命令上传 Functions 项目目录的内容。 如果在本地删除文件，`publish` 命令不会将文件从 Azure 中删除。 可以使用 [Azure 门户]中的 [Kudu 工具](functions-how-to-use-azure-function-app-settings.md#kudu)删除 Azure 中的文件。

## <a name="next-steps"></a>后续步骤

Azure Functions Core Tools 是[开源工具且托管在 GitHub 上](https://github.com/azure/azure-functions-cli)。  
若要提交 bug 或功能请求，[请打开 GitHub 问题](https://github.com/azure/azure-functions-cli/issues)。 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 门户]: https://portal.azure.com 

