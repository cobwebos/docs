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
ms.date: 06/08/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 478c2ffbb0bfe5b1595bc6ea1bebb2144aebc728
ms.contentlocale: zh-cn
ms.lasthandoff: 06/09/2017

---
# <a name="code-and-test-azure-functions-locally"></a>在本地对 Azure 函数进行编码和测试

可使用你最喜欢的代码编辑器和本地开发工具在本地运行 Azure Functions 运行时。 在 Azure 中触发事件并调试 C# 和 JavaScript 函数。

首先，从 npm 安装 [Azure Functions Core Tools]。 Azure Functions Core Tools 是 Azure Functions 运行时的本地版本，可在本地 Windows 计算机上运行。 它既不是仿真器，也不是模拟器。 它与在 Azure 中运行的运行时相同。

[Azure Functions Core Tools] 增加以下命令别名：
- `func`
- `azfun`
- `azurefunctions`

Azure Functions Core Tools 是[开源工具且托管在 GitHub 上](https://github.com/azure/azure-functions-cli)。 若要提交 bug 或功能请求，[请打开 GitHub 问题](https://github.com/azure/azure-functions-cli/issues)。

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
  "IsEncrypted": false,   // If set to true, all values are encrypted by using a local machine key. Use with "func settings" commands.
  "Values": {
    "AzureWebJobsStorage": "<connection string>",   // This is required for all triggers except HTTP.
    "AzureWebJobsDashboard": "<connection string>", // Optional, controls whether to log to the Monitor tab in the portal.
  },
  "Host": {
    "LocalHttpPort": 7071, // If specified, this is the default port for "host start" and "run". Can be overridden by using the --port command-line option.
    "CORS": "*"            // Origins to allow in the CORS setting.
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

在“值”集合中指定应用设置。 然后，可将这些设置读取为环境变量。 在 C# 中，使用 `System.Environment.GetEnvironmentVariable` 或 `ConfigurationManager.AppSettings`。 在 JavaScript 中，使用 `process.env`。 如果将相同的设置指定为系统环境变量，则该设置优先于 local.settings.json 中的值。

应用设置 AzureWebJobsStorage 是 Azure Functions 运行时所有触发器（除 HTTP 外）所需的特殊设置。 运行时在内部创建队列，从而管理此存储帐户中的触发器。 如果未指定 AzureWebJobsStorage 的某个值，且使用的是除 HTTP 以外的触发器，则会出现以下消息：

*local.settings.json 中的 AzureWebJobsStorage 缺少值。该值对除 HTTP 以外的所有触发器都是必需的。可运行“func azure functionapp fetch-app-settings <functionAppName>”或在 local.settings.json 中指定连接字符串*。

> [!NOTE]
> 可通过连接字符串 "AzureWebJobsStorage": "UseDevelopmentStorage=true" 使用本地存储仿真器。 但是，与 Azure 存储服务相比，行为可能会存在差异。

以下设置可自定义本地 Functions 主机：
- `LocalHttpPort`。 用于 `func host start``func run` 的默认端口。 `--port` 命令行选项优先于此值。
- `CORS`。 CORS 允许的来源，作为以逗号分隔的列表，其中不含空格。 使用“*”，允许所有来源。

可在 `ConnectionStrings` 对象中提供连接字符串。 使用提供程序名称 System.Data.SqlClient 将它们添加到环境中。

大多数触发器和绑定都有一个 Connection 属性，它是 local.settings.json 中环境变量或应用设置的名称。 如果应用设置值丢失，则会出现以下消息：

*警告：在 local.settings.json 中找不到名为“MyStorageConnection”的值，该值与“BlobTriggerCSharp\function.json”中“blobTrigger”上设置的“connection”属性相匹配。可运行“func azure functionapp fetch-app-settings <functionAppName>”或在 local.settings.json 中指定连接字符串*。

文件 local.settings.json 仅由 Azure Functions Core Tools 使用。 若要在 Azure 中设置应用设置和连接字符串，请使用“应用程序设置”边栏选项卡。

### <a name="configure-app-settings"></a>配置应用设置

若要设置连接字符串的值，可执行以下任一操作：
- 手动输入 [Azure 存储资源管理器](http://storageexplorer.com/)中的连接字符串。
- 请使用 func azure functionapp fetch-app-settings \<FunctionAppName\>。 需要登录 Azure。
- 使用 func azure functionapp storage fetch-connection-string \<StorageAccountName\>。 需要登录 Azure。

## <a name="create-a-function"></a>创建函数

若要创建函数，请运行 `func new`。 此命令包括以下可选参数：

- `--language [-l]`。 C#、F# 或 JavaScript 等模板编程语言。
- `--template [-t]`。 模板名称。
- `--name [-n]`。 函数名称。

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

可通过 `func host start` 使用以下选项：

- `--port [-p]`。 要侦听的本地端口。 默认值：7071。
- `--debug <type>`。 选项为 VSCode 和 VS。
- `--cors`。 以逗号分隔的 CORS 来源列表，其中不包含空格。
- `--nodeDebugPort [-n]`。 节点调试程序要使用的端口。 默认值：launch.json 中的值或 5858。
- `--debugLevel [-d]`。 控制台跟踪级别（关闭、详情、信息、警告或错误）。 默认：信息。
- `--timeout [-t]`。 Functions 主机启动的超时时间（以秒为单位）。 默认值：20 秒。
- `--useHttps`。 绑定到 https://localhost:{port}，而非 http://localhost:{port}。 默认情况下，此选项会在计算机上创建可信证书。
- `--pause-on-error`。 退出进程前，暂停增加其他输入。 十分适用于从集成开发环境 (IDE) 启动 Azure Functions Core Tools 的情况。

Functions 主机启动时，会输出 HTTP 触发的函数的 URL：

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug"></a>调试

若要附加调试程序，请传递 `--debug` 参数。 若要调试 JavaScript 函数，请使用 Visual Studio Code。 对于 C# 函数，请使用 Visual Studio。

若要调试 C# 函数，请使用 `--debug vs`。 或者，使用 [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/)。 

若要启动主机并设置 JavaScript 调试，运行：

```
func host start --debug vscode
```

然后，在 Visual Studio Code 中的“调试”视图中，选择“附加到 Azure Functions”。 可附加断点、检查变量和逐步执行代码。

![使用 Visual Studio Code 调试 JavaScript](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="call-a-function-by-using-test-data"></a>使用测试数据调用函数

也可直接使用 `func run <FunctionName>` 调用函数。 此命令与 Azure 门户中的“测试”选项卡类似，可在其中为函数提供输入数据。 此命令启动整个 Functions 主机。

可通过 `func run` 使用以下选项：

- `--content [-c]`。 内联内容。
- `--debug [-d]`。 运行函数前，将调试程序附加到主机进程。
- `--timeout [-t]`。 本地 Functions 主机准备就绪前的等待时间（以秒为单位）。
- `--file [-f]`。 要用作内容的文件名。
- `--no-interactive`。 不提示输入。 适用于自动化方案。

例如，若要调用 HTTP 触发的函数并传递内容正文，请运行以下命令：

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-a-function-app"></a>发布函数应用

若要将 Functions 项目发布到 Azure 中的函数应用，使用 `publish` 命令：

```
func azure functionapp publish <FunctionAppName>
```

可以使用以下选项：

- `--publish-local-settings [-i]`。  将 local.settings.json 中的设置发布到 Azure，如果该设置已存在，则提示进行覆盖。
- `--overwrite-settings [-y]`。 必须与 `-i` 一起使用。 如果不同，则使用本地值覆盖 Azure 中的 AppSettings。 默认为提示。

`publish` 命令上传 Functions 项目目录的内容。 如果本地删除文件，此命令不会将它们从 Azure 删除。 若要删除这些文件，请在 Azure Functions 门户中使用 Kudu。 若要启动 Kudu，请在 Azure Functions 门户中，依次选择“平台功能” > “高级工具 (Kudu)”。 


<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools

