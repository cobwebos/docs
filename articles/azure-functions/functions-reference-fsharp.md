---
title: "Azure Functions F # 开发人员参考 | Microsoft Docs"
description: "了解如何开发使用 F # 的 Azure 功能。"
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务器体系结构, F#"
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: functions
ms.devlang: fsharp
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/09/2016
ms.author: syclebsc
ms.openlocfilehash: 039306b093d92b66883edcca10e42f7b1dbc7245
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F# 开发人员参考
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

Azure Functions F# 是用于在云中轻松运行小段代码或“函数”的一个解决方案。 数据通过函数参数流入 F # 函数。 在 `function.json`指定参数名称，没有访问函数记录器和取消令牌等的预定义的名称。

本文假定已阅读 [Azure Functions 开发人员参考](functions-reference.md)。

## <a name="how-fsx-works"></a>.Fsx 的工作原理
`.fsx` 文件是 F # 脚本。 它可以被视为包含单个文件的 F # 项目。 该文件包含程序（在此情况下，Azure 函数）和管理依赖关系的指令的代码。

如果使用 Azure 函数的 `.fsx`，通常需要自动包含程序集，以便可以专注于函数而不是“样本”代码。

## <a name="binding-to-arguments"></a>绑定到参数
对于每个绑定支持某些参数，请参阅 [Azure 函数触发器和绑定开发人员参考](functions-triggers-bindings.md)。 例如，blob 触发器支持的其中一个参数绑定是 POCO，可以使用 F # 记录来表示。 例如：

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

F # Azure 函数采用一个或多个参数。 所谓 Azure 函数参数时，指的是 *输入* 参数和 *输出* 参数。 顾名思义，输入参数就是输入到 F # Azure 函数的参数。 *输出* 参数是可变的数据或 `byref<>` 参数就是*从*返回数据的参数。

在以上示例中，`blob` 是输入参数，`output` 是输出参数。 注意，针对 `output`，请使用 `byref<>` （无需添加 `[<Out>]` 批注）。 使用 `byref<>` 类型允许函数更改参数所引用的记录或对象。

作为输入类型使用 F # 记录时，必须使用 `[<CLIMutable>]`标记的记录定义，以便在记录传递给函数之前让 Azure 功能框架设置相应字段。 实质上， `[<CLIMutable>]` 生成记录属性的 setter。 例如：

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F # 类还可用于输入和输出参数。 对于类，属性通常需要 getter 和 setter。 例如：

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>日志记录
若要记录以 F # 输出传送到 [流式传输日志](../app-service/web-sites-enable-diagnostic-log.md) 中，函数应采取 `TraceWriter` 参数。 为了保持一致，我们建议参数名为 `log`。 例如：

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>异步
可使用 `async` 工作流，但结果需要返回 `Task`。 使用 `Async.StartAsTask` 可实现此目的，例如：

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>取消令牌
如果函数需要正常地处理关闭，可指定 [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) 参数。 联合 `async` 可实现此目的，例如：

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>导入命名空间
可按照通常的处理方式打开命名空间：

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

自动打开以下命名空间：

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`。

## <a name="referencing-external-assemblies"></a>引用外部程序集
与此类似，可以使用 `#r "AssemblyName"` 指令添加框架程序集引用。

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

由 Azure 函数主机环境自动添加以下程序集：

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`。

此外，以下程序集比较特殊，可能由 simplename 引用 (例如 `#r "AssemblyName"`)：

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`。

如果需要引用私有程序集，可以将程序集文件上传到  `bin` 与功能相关的文件，并通过使用文件名（例如`#r "MyAssembly.dll"`）来引用它. 有关如何将文件上传到函数文件夹的信息，请参阅下一部分中有关程序包管理的信息。

## <a name="editor-prelude"></a>Editor Prelude
支持 F # 编译器服务的编辑器检测不到命名空间和 Azure 函数会自动包括在内的程序集。 在这种情况下，包含可帮助找到使用的程序集并显式地打开命名空间的 prelude，这会很有帮助。 例如：

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Azure 函数执行代码时，它可以处理带有 `COMPILED` 定义的源，因此将忽略编辑器 prelude。

<a name="package"></a>

## <a name="package-management"></a>包管理
若要在 F# 函数中使用 NuGet 包，可将 `project.json` 文件添加到函数应用的文件系统中函数的文件夹。 下面是一个示例 `project.json` 文件，其中将 NuGet 包引用添加到 `Microsoft.ProjectOxford.Face` 1.1.0 版：

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

只支持.NET Framework 4.6，因此请确保 `project.json` 文件指定 `net46`如下所示。

上传 `project.json` 文件时，运行时获取包，并自动将引用添加到包程序集。 无需添加 `#r "AssemblyName"` 指令。 只需添加所需 `open` 语句到 `.fsx` 文件。

可能希望会自动引用程序集放入编辑器 prelude，以提高编辑器与 F # 编译服务的交互。

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>如何添加 `project.json` 文件到 Azure 函数
1. 首先，确保函数应用程序正在运行，可以通过在 Azure 门户中打开函数来执行此操作。 通过此操作，还可以访问显示程序包安装输出位置的流式日志日志。
2. 若要上传 `project.json` 文件，请使用如何更新函数应用程序文件 中描述的其中一[种方法](functions-reference.md#fileupdate)。 如果使用 [ Azure 函数的连续部署](functions-continuous-deployment.md) ，可以添加 `project.json` 文件到临时分支，以便添加到部署的分支文件对其进行测试。
3. 添加 `project.json` 文件后，将看到类似于函数流式日志中的实例的输出：

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>环境变量
若要获取环境变量或某个应用程序设置值，请使用 `System.Environment.GetEnvironmentVariable`，例如：

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>重用.fsx 代码
可以通过 `#load` 指令使用其他 `.fsx` 文件中的代码。 例如：

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

提供给 `#load` 指令的路径与`.fsx` 文件位置相关。

* `#load "logger.fsx"` 加载函数文件夹中的文件。
* `#load "package\logger.fsx"` 加载文件 `package` 函数文件夹中的文件夹。
* `#load "..\shared\mylogger.fsx"` 在同一级别（即 `wwwroot` 的正下方）加载 `shared` 文件夹中的文件，使其成为函数文件夹。

`#load` 指令只适用于 `.fsx`（F # 脚本）文件，而不适用于 `.fs` 文件。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [F # 指南](/dotnet/articles/fsharp/index)
* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [ Azure Functions 测试](functions-test-a-function.md)
* [Azure Functions 缩放](functions-scale.md)

