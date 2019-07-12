---
title: Azure Functions 的 PowerShell 开发人员参考
description: 了解如何使用 PowerShell 来挖掘一些功能。
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a75bdaf0e26193a5b2792b52923c085eff89b83f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706401"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell 开发人员指南

本文提供了有关如何编写使用 PowerShell 的 Azure Functions 的详细信息。

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

PowerShell Azure 函数 （函数） 都表示为执行时触发的 PowerShell 脚本。 每个函数脚本具有相关`function.json`定义函数的行为方式，例如触发方式的文件和其输入和输出参数。 若要了解详细信息，请参阅[触发器和绑定文章](functions-triggers-bindings.md)。 

PowerShell 脚本函数中定义的所有输入绑定的名称匹配的参数中其他类型的函数，如采用`function.json`文件。 一个`TriggerMetadata`参数还传递包含启动该函数的触发器的其他信息。

本文假定你已阅读 [Azure Functions 开发人员参考](functions-reference.md)。 您应该还完成[Functions 快速入门适用于 PowerShell](functions-create-first-function-powershell.md)创建第一个 PowerShell 函数。

## <a name="folder-structure"></a>文件夹结构

PowerShell 项目的所需的文件夹结构如下所示。 可更改此默认值。 有关详细信息，请参阅下面的 [scriptFile](#configure-function-scriptfile) 部分。

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

在项目的根目录，一开始是共享[ `host.json` ](functions-host-json.md)可用于配置函数应用的文件。 每个函数有具有其自己的代码文件 (.ps1) 和绑定配置文件的文件夹 (`function.json`)。 Function.json 文件父目录的名称始终是函数的名称。

某些绑定需要存在`extensions.csproj`文件。 绑定中时所需的扩展[版本 2.x](functions-versions.md) Functions 运行时，定义在`extensions.csproj`文件，并且在实际的库文件`bin`文件夹。 本地开发时，必须[注册绑定扩展](functions-bindings-register.md#extension-bundles)。 在 Azure 门户中开发函数时，系统将为你完成此注册。

在 PowerShell 函数应用中，您可以选择可能`profile.ps1`用于运行函数应用开始运行时 (也称为 *[冷启动](#cold-start)* 。 有关详细信息，请参阅[PowerShell 配置文件](#powershell-profile)。

## <a name="defining-a-powershell-script-as-a-function"></a>为函数定义 PowerShell 脚本

默认情况下，Functions 运行时会在 `run.ps1` 中查找你的函数，其中，`run.ps1` 与其相应的 `function.json` 共享同一个父目录。

您的脚本将执行传递参数的数目。 若要处理这些参数，将添加`param`到顶部的您如以下示例所示的脚本块：

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata 参数

`TriggerMetadata`参数用于提供有关触发器的其他信息。 其他元数据各不相同绑定绑定，但它们都包含`sys`属性，其中包含以下数据：

```powershell
$TriggerMetadata.sys
```

| 属性   | 说明                                     | type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Utc 格式，触发该函数是时，        | DateTime |
| MethodName | 已触发的函数的名称     | string   |
| RandGuid   | 将唯一 guid 传递给此函数的执行 | string   |

每个触发器类型具有一组不同的元数据。 例如，`$TriggerMetadata`有关`QueueTrigger`包含`InsertionTime`， `Id`， `DequeueCount`，等等。 队列触发器的元数据的详细信息，请转到[的队列触发器的官方文档](functions-bindings-storage-queue.md#trigger---message-metadata)。 在查看文档[触发器](functions-triggers-bindings.md)您正在使用以查看即将出现的内部触发器元数据。

## <a name="bindings"></a>绑定

在 PowerShell 中，[绑定](functions-triggers-bindings.md)配置并在函数的 function.json 中定义。 函数通过多种方式来与绑定交互。

### <a name="reading-trigger-and-input-data"></a>读取触发器和输入数据

触发器和输入的绑定读取作为参数传递到函数。 输入的绑定具有`direction`设置为`in`function.json 中。 `name`中定义的属性`function.json`是中的参数名称`param`块。 由于 PowerShell 绑定使用命名的参数，参数的顺序并不重要。 但是，它是最佳做法遵循的顺序中定义的绑定`function.json`。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>写入输出数据

在函数中，已将输出绑定`direction`设置为`out`function.json 中。 可以使用输出绑定到编写`Push-OutputBinding`cmdlet，后者适用于 Functions 运行时。 在所有情况下，`name`属性中定义的绑定`function.json`对应于`Name`参数的`Push-OutputBinding`cmdlet。

以下示例演示如何调用`Push-OutputBinding`函数脚本中：

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

您还可以通过管道的特定绑定传递的值。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` 根据指定的值不同的行为`-Name`:

* 当指定的名称不能解析为有效的输出绑定时，将引发错误。

* 在输出绑定接受的值的集合，可以调用`Push-OutputBinding`反复将推送多个值。

* 在输出绑定仅接受单一实例值后，调用`Push-OutputBinding`第二次引发错误。

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` 语法

以下是有效的参数以调用`Push-OutputBinding`:

| 名称 | 类型 | 位置 | 描述 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 要设置的输出绑定的名称。 |
| **`-Value`** | Object | 2 | 输出绑定的值你想要设置，这通过管道 ByValue 接受的。 |
| **`-Clobber`** | SwitchParameter | 名为 | （可选）如果指定，会强制要设置为指定的输出绑定的值。 | 

此外支持以下常见参数： 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

有关详细信息，请参阅[有关 CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)。

#### <a name="push-outputbinding-example-http-responses"></a>推送 OutputBinding 示例：HTTP 响应

HTTP 触发器将返回使用输出绑定，名为的响应`response`。 在以下示例中，输出绑定的`response`"output #1"的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

输出是为 HTTP，接受单一实例值，因为出现错误时引发`Push-OutputBinding`第二次调用。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

对于只接受单一实例值的输出，可以使用`-Clobber`参数来重写而不是尝试添加到集合的旧值。 下面的示例假定你已添加了一个值。 通过使用`-Clobber`，从下面的示例响应重写要返回的值为"output #3"的现有值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>推送 OutputBinding 示例：队列输出绑定

`Push-OutputBinding` 用于将数据发送到输出绑定，如[Azure 队列存储输出绑定](functions-bindings-storage-queue.md#output)。 在以下示例中，向队列写入消息的值为"output #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

存储队列输出绑定接受多个输出值。 第一个具有两个项的列表写入队列后，在这种情况下，调用下面的示例:"output #1"和"输出 #2"。

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

以下示例中前, 两个后调用时将添加到输出集合的两个多个值：

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

该消息写入队列，包含四个值:"output #1"、"输出 #2"、"输出 #3"和"输出 #4"。

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` Cmdlet

可以使用`Get-OutputBinding`cmdlet 来检索当前设置的输出绑定的值。 此 cmdlet 检索包含各自的值与输出绑定的名称的哈希表。 

下面是使用的示例`Get-OutputBinding`返回当前绑定值：

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` 此外包含名为的参数`-Name`，这可以用于筛选返回的绑定，如以下示例所示：

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

中支持通配符 （*） `Get-OutputBinding`。

## <a name="logging"></a>日志记录

PowerShell 函数中的日志记录的工作方式类似常规 PowerShell 日志记录。 日志记录 cmdlet 可用于写入到每个输出流。 每个 cmdlet 将映射到函数所使用的日志级别。

| 日志记录级别的函数 | 日志记录 cmdlet |
| ------------- | -------------- |
| Error | **`Write-Error`** |
| 警告 | **`Write-Warning`**  | 
| 信息 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 信息 | 将写入到_信息_级别日志记录。 |
| 调试 | **`Write-Debug`** |
| 跟踪 | **`Write-Progress`** <br /> **`Write-Verbose`** |

除了这些 cmdlet，向管道写入内容重定向到`Information`日志级别，并显示时带有默认 PowerShell 格式设置。

> [!IMPORTANT]
> 使用`Write-Verbose`或`Write-Debug`cmdlet 不足够，以查看详细和调试级别日志记录。 此外必须配置的日志级别阈值，声明的真正关心的日志级别。 若要了解详细信息，请参阅[配置函数应用日志级别](#configure-the-function-app-log-level)。

### <a name="configure-the-function-app-log-level"></a>配置 function app 日志级别

Azure Functions，可定义的阈值级别，以使其很容易地控制方式函数写入日志。 若要设置的阈值为写入到控制台的所有跟踪，请使用`logging.logLevel.default`中的属性[`host.json`文件][host.json 参考]。 此设置应用于 Function App 中的所有函数。

下面的示例设置的阈值，若要启用详细日志记录的所有函数，但若要启用一个名为函数的调试日志记录将阈值设置`MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

有关详细信息，请参阅 [host.json 参考]。

### <a name="viewing-the-logs"></a>查看日志

如果在 Azure 中运行函数应用，可以使用 Application Insights 进行监视。 若要了解有关查看和查询函数日志的详细信息，请阅读[监视 Azure Functions](functions-monitoring.md)。

如果要运行本地开发在 Function App，记录到文件系统默认值。 若要查看在控制台中的日志，请设置`AZURE_FUNCTIONS_ENVIRONMENT`环境变量为`Development`之前启动 Function App。

## <a name="triggers-and-bindings-types"></a>触发器和绑定类型

有可供你使用与函数应用的触发器和绑定数。 触发器和绑定的完整列表[可在此处找到](functions-triggers-bindings.md#supported-bindings)。

所有触发器和绑定为几个真实的数据类型都表示在代码中：

* Hashtable
* string
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

此列表中的前五个类型是标准.NET 类型。 最后两个仅由[HttpTrigger 触发器](#http-triggers-and-bindings)。

在函数中的每个绑定参数必须是以下类型之一。

### <a name="http-triggers-and-bindings"></a>HTTP 触发器和绑定

HTTP 和 webhook 触发器以及 HTTP 输出绑定使用请求和响应对象来表示 HTTP 消息。

#### <a name="request-object"></a>请求对象

传递到该脚本的请求对象的类型是`HttpRequestContext`，其中包含以下属性：

| 属性  | 说明                                                    | 类型                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 一个包含请求正文的对象。 `Body` 序列化为基于数据的最佳类型。 例如，如果数据为 JSON 时，它在作为传递哈希表。 如果数据是一个字符串，它是在作为字符串传递。 | object |
| **`Headers`** | 一个包含请求标头的字典。                | Dictionary<string,string><sup>*</sup> |
| **`Method`** | 请求的 HTTP 方法。                                | string                    |
| **`Params`**  | 一个包含请求的路由参数的对象。 | Dictionary<string,string><sup>*</sup> |
| **`Query`** | 一个包含查询参数的对象。                  | Dictionary<string,string><sup>*</sup> |
| **`Url`** | 请求的 URL。                                        | string                    |

<sup>*</sup> 所有`Dictionary<string,string>`密钥不区分大小写。

#### <a name="response-object"></a>响应对象

您应发回的响应对象的类型是`HttpResponseContext`，其中包含以下属性：

| 属性      | 说明                                                 | 类型                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 一个包含响应正文的对象。           | object                    |
| **`ContentType`** | 设置响应的内容类型的简称。 | string                    |
| **`Headers`** | 一个包含响应标头的对象。               | 字典或哈希表   |
| **`StatusCode`**  | 响应的 HTTP 状态代码。                       | 字符串或整数             |

#### <a name="accessing-the-request-and-response"></a>访问请求和响应

当使用 HTTP 触发器时，可以访问 HTTP 请求相同的方式与任何其他输入绑定。 它位于`param`块。

使用`HttpResponseContext`对象返回响应，如在下面的示例所示：

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

调用此函数的结果将是：

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>有关触发器和绑定的类型强制转换

对于某些像 blob 绑定的绑定，您可以指定参数的类型。

例如，若要从 Blob 存储以字符串形式提供的数据，添加以下类型强制转换为我`param`块：

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 配置文件

在 PowerShell 中，是一个 PowerShell 配置文件的概念。 如果您不熟悉 PowerShell 配置文件，请参阅[有关配置文件](/powershell/module/microsoft.powershell.core/about/about_profiles)。

在 PowerShell 函数，该配置文件脚本执行函数应用启动时。 函数应用都是启动首次部署时和之后在空闲状态 ([冷启动](#cold-start))。

创建使用工具，如 Visual Studio Code 和 Azure Functions Core Tools，默认值的函数应用时`profile.ps1`为你创建。 保留默认配置文件[上的核心工具 GitHub 存储库](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1)和包含：

* 到 Azure 的自动 MSI 身份验证。
* 若要打开 Azure PowerShell 的功能`AzureRM`如果想要的 PowerShell 别名。

## <a name="powershell-version"></a>PowerShell 版本

下表显示了每个主要版本的 Functions 运行时使用的 PowerShell 版本：

| Functions 版本 | PowerShell 版本                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 （由运行时锁定） |
| 2.x               | PowerShell Core 6                              |

可以看到当前版本，通过打印`$PSVersionTable`从任何函数。

## <a name="dependency-management"></a>依赖项管理

PowerShell 函数支持由服务进行管理的 Azure 模块。 通过修改 host.json 和 managedDependency 启用属性设置为 true，将处理 requirements.psd1 文件。 最新的 Azure 模块将自动下载并提供给该函数。

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

利用自己的自定义模块或[PowerShell 库](https://powershellgallery.com)比如何，所做的那样通常稍有不同。

当在本地计算机上安装模块时，将在一个全局可用文件夹中你`$env:PSModulePath`。 在 Azure 中运行你的函数，因为不会有权在计算机上安装的模块。 这就要求`$env:PSModulePath`PowerShell 函数应用不同于`$env:PSModulePath`常规的 PowerShell 脚本中。

在函数中，`PSModulePath`包含两个路径：

* 一个`Modules`Function App 的根目录下存在的文件夹。
* 路径`Modules`文件夹位于 PowerShell 语言工作。

### <a name="function-app-level-modules-folder"></a>函数应用级别`Modules`文件夹

若要从 PowerShell 库中使用自定义模块或 PowerShell 模块，可以将放置你的函数所依赖的中的模块`Modules`文件夹。 从此文件夹中，模块会自动提供给 functions 运行时。 函数应用中的任何函数可以使用这些模块。

若要充分利用此功能，创建`Modules`function app 的根目录中的文件夹。 保存要在此位置在函数中使用的模块。

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

使用`Save-Module`若要保存的所有模块函数使用，或复制到您自己自定义模块`Modules`文件夹。 与模块文件夹中，函数应用应具有以下文件夹结构：

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

在启动 function app 时，PowerShell 语言辅助角色增加这`Modules`文件夹到`$env:PSModulePath`，以便您可以依赖模块自动加载，就像在常规的 PowerShell 脚本中。

### <a name="language-worker-level-modules-folder"></a>语言工作级别`Modules`文件夹

由 PowerShell 语言工作线程通常用于多个模块。 这些模块中的最后一个位置定义`PSModulePath`。 

模块的当前列表如下所示：

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive)： 使用用于存档，如模块`.zip`， `.nupkg`，等等。
* **ThreadJob**:PowerShell 作业 Api 基于线程的实现。

函数使用这些模块的最新版本。 若要使用这些模块的特定版本，您可以将特定版本放入`Modules`函数应用的文件夹。

## <a name="environment-variables"></a>环境变量

在 Functions 中，服务连接字符串等[应用设置](functions-app-settings.md)在执行过程中将公开为环境变量。 您可以访问这些设置，请使用`$env:NAME_OF_ENV_VAR`，如以下示例所示：

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在本地运行时，可从 [local.settings.json](functions-run-local.md#local-settings-file) 项目文件读取应用设置。

## <a name="concurrency"></a>并发

默认情况下，函数 PowerShell 运行时只能处理一次一个调用的函数。 但是，此并发级别可能在以下情况下不足：

* 当想要同时处理大量的调用。
* 当必须调用同一函数应用中的其他函数的函数。

可以通过将以下环境变量设置为一个整数值来更改此行为：

```
PSWorkerInProcConcurrencyUpperBound
```

设置此环境变量[应用设置](functions-app-settings.md)的 Function App。

### <a name="considerations-for-using-concurrency"></a>使用并发的注意事项

PowerShell 是_单线程_默认脚本语言。 但是，可以通过在同一进程中使用多个 PowerShell 运行空间添加并发。 此功能是 Azure Functions PowerShell 运行时的工作原理。

有一些使用此方法的缺点。

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>并发的好坏都作为运行的计算机

如果在上运行函数应用[应用服务计划](functions-scale.md#app-service-plan)，仅支持单核，那么并发性不会帮助很多。 这是因为有没有更多的内核数，以帮助平衡负载。 在这种情况下，性能的好坏时的单一核心上下文切换之间的运行空间。

[消耗计划](functions-scale.md#consumption-plan)使用只有一个核心，因此您不能利用并发运行。 如果你想要充分利用并发性，而是将函数部署到具有足够的核心的专用应用服务计划上运行的函数应用。

#### <a name="azure-powershell-state"></a>Azure PowerShell 状态

Azure PowerShell 使用某些_进程级别_上下文和状态，以帮助节省您从过多键入。 但是，如果函数应用中启用并发并调用一些操作，更改状态时，您最终可能争用条件。 这些争用条件很难调试，因为一次调用依赖于某个特定状态和其他调用更改状态。

有使用 Azure PowerShell 的并发是时间的出巨大价值，因为某些操作可能要花费相当长。 但是，您必须谨慎。 如果您怀疑遇到争用条件，设置并发回`1`，然后重试请求。

## <a name="configure-function-scriptfile"></a>配置函数 `scriptFile`

默认情况下，PowerShell 函数执行从`run.ps1`，文件共享作为其对应的同一个父目录`function.json`。

`scriptFile`中的属性`function.json`可用于获取文件夹结构，如以下示例所示：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

在这种情况下，`function.json`有关`myFunction`包括`scriptFile`引用文件的要运行的导出函数的属性。

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>使用 PowerShell 模块通过配置入口点

这篇文章表明 PowerShell 函数，在默认`run.ps1`模板生成的脚本文件。
但是，还可以包含 PowerShell 模块中的函数。 可以使用引用该模块中的特定函数代码`scriptFile`和`entryPoint`function.json 中的字段配置文件。

在这种情况下，`entryPoint`是函数或 cmdlet 中引用的 PowerShell 模块中的名称`scriptFile`。

请考虑以下文件夹结构：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

其中`PSFunction.psm1`包含：

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

在此示例中，为配置`myFunction`包括`scriptFile`属性，引用`PSFunction.psm1`，这是另一个文件夹中的 PowerShell 模块。  `entryPoint`属性引用`Invoke-PSTestFunc`函数，是模块中的入口点。

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

使用此配置，`Invoke-PSTestFunc`执行完全按照`run.ps1`会。

## <a name="considerations-for-powershell-functions"></a>PowerShell 函数的注意事项

使用 PowerShell 函数时，应注意以下各节中的注意事项。

### <a name="cold-start"></a>冷启动

当开发中的 Azure Functions[无服务器托管模型](functions-scale.md#consumption-plan)、 冷启动已成为现实。 *冷启动*指的是时间段内它花费的 function app 以开始运行用于处理请求。 冷启动发生更频繁地消耗计划，因为在处于非活动状态的时间段期间将 function app 会关闭。

### <a name="bundle-modules-instead-of-using-install-module"></a>而不是使用捆绑包模块 `Install-Module`

每次调用运行您的脚本。 避免使用`Install-Module`在脚本中。 改为使用`Save-Module`才能发布，以便你的函数无需浪费时间去下载该模块。 冷启动会影响你的函数，请考虑部署到 function app[应用服务计划](functions-scale.md#app-service-plan)设置为*alwayson*或设置为[高级计划](functions-scale.md#premium-plan)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)

[host.json 参考]: functions-host-json.md
