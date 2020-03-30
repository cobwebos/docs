---
title: Azure 函数的 PowerShell 开发人员参考
description: 了解如何使用 PowerShell 开发功能。
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276733"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure 函数 PowerShell 开发人员指南

本文详细介绍了如何使用 PowerShell 编写 Azure 函数。

PowerShell Azure 函数（函数）表示为 PowerShell 脚本，该脚本在触发时执行。 每个函数脚本都有一个`function.json`相关文件，用于定义函数的运行方式，例如其触发方式及其输入和输出参数。 要了解更多信息，请参阅[触发器和绑定文章](functions-triggers-bindings.md)。 

与其他类型的函数一样，PowerShell 脚本函数采用的参数与`function.json`文件中定义的所有输入绑定的名称匹配。 还会`TriggerMetadata`传递一个参数，其中包含启动函数的触发器的其他信息。

本文假定你已阅读 [Azure Functions 开发人员参考](functions-reference.md)。 您还应完成[PowerShell 的功能快速启动](functions-create-first-function-powershell.md)，以创建您的第一个 PowerShell 功能。

## <a name="folder-structure"></a>文件夹结构

PowerShell 项目所需的文件夹结构如下所示。 可更改此默认值。 有关详细信息，请参阅下面的 [scriptFile](#configure-function-scriptfile) 部分。

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

在项目的根目录，有一个共享[`host.json`](functions-host-json.md)文件，可用于配置函数应用。 每个函数都有一个文件夹，其中包含其自己的代码文件 （.ps1）`function.json`和绑定配置文件 （）。 函数.json 文件的父目录的名称始终是函数的名称。

某些绑定需要文件的存在`extensions.csproj`。 在`extensions.csproj`文件中定义在文件中所需的[2.x 版本和更高版本的](functions-versions.md)函数运行时所需的绑定扩展名，并随`bin`文件夹中的实际库文件一起定义。 本地开发时，必须[注册绑定扩展](functions-bindings-register.md#extension-bundles)。 在 Azure 门户中开发函数时，系统将为你完成此注册。

在 PowerShell 函数应用中，您可以选择在函数应用`profile.ps1`开始运行时运行的 ，否则称为*[冷启动](#cold-start)*。 有关详细信息，请参阅[PowerShell 配置文件](#powershell-profile)。

## <a name="defining-a-powershell-script-as-a-function"></a>将 PowerShell 脚本定义为函数

默认情况下，Functions 运行时会在 `run.ps1` 中查找你的函数，其中，`run.ps1` 与其相应的 `function.json` 共享同一个父目录。

脚本在执行时传递了许多参数。 要处理这些参数，请向脚本`param`顶部添加一个块，如以下示例所示：

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>触发元数据参数

该`TriggerMetadata`参数用于提供有关触发器的其他信息。 其他元数据因绑定而异，但它们都包含包含以下数据`sys`的属性：

```powershell
$TriggerMetadata.sys
```

| properties   | 描述                                     | 类型     |
|------------|-------------------------------------------------|----------|
| UtcNow     | 在 UTC 中触发函数时        | DateTime |
| MethodName | 触发的函数的名称     | 字符串   |
| 兰德吉德   | 函数的此执行的唯一 guid | 字符串   |

每个触发器类型都有一组不同的元数据。 `$TriggerMetadata`例如，`QueueTrigger`的 包含`InsertionTime`， `Id` `DequeueCount`、 、 等。 有关队列触发器元数据的详细信息，请访问[队列触发器的官方文档](functions-bindings-storage-queue-trigger.md#message-metadata)。 检查您正在使用[的触发器](functions-triggers-bindings.md)的文档，查看触发器元数据中的内容。

## <a name="bindings"></a>绑定

在 PowerShell 中，[绑定](functions-triggers-bindings.md)在函数的函数中配置和定义。 函数通过多种方式来与绑定交互。

### <a name="reading-trigger-and-input-data"></a>读取触发器和输入数据

触发器和输入绑定作为传递给函数的参数读取。 输入绑定具有函数`direction``in`.json 中的集。 中`name``function.json`定义的属性是`param`块中参数的名称。 由于 PowerShell 使用命名参数进行绑定，因此参数的顺序并不重要。 但是，最佳做法是遵循 中定义的绑定的顺序`function.json`。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>写入输出数据

在函数中，输出绑定在`direction`函数.json 中具有一`out`个集。 您可以使用`Push-OutputBinding`cmdlet 写入输出绑定，cmdlet 可用于函数运行时。 `name`在所有情况下，`function.json`中定义的绑定的属性对应于`Name``Push-OutputBinding`cmdlet 的参数。

下面显示了如何在函数脚本中`Push-OutputBinding`调用：

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

您还可以通过管道传递特定绑定的值。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`根据为`-Name`指定的值，行为方式不同：

* 当指定的名称无法解析为有效的输出绑定时，则引发错误。

* 当输出绑定接受值集合时，可以重复调用`Push-OutputBinding`以推送多个值。

* 当输出绑定仅接受单例值时，调用`Push-OutputBinding`第二次会产生错误。

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` 语法

以下是用于调用`Push-OutputBinding`的有效参数：

| “属性” | 类型 | 位置 | 描述 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 要设置的输出绑定的名称。 |
| **`-Value`** | Object | 2 | 要设置的输出绑定的值，从管道 ByValue 接受。 |
| **`-Clobber`** | SwitchParameter | 已命名 | （可选）指定时，强制为指定的输出绑定设置值。 | 

还支持以下常见参数： 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

有关详细信息，请参阅[有关通用参数](https://go.microsoft.com/fwlink/?LinkID=113216)。

#### <a name="push-outputbinding-example-http-responses"></a>推送输出绑定示例：HTTP 响应

HTTP 触发器使用名为 的`response`输出绑定返回响应。 在下面的示例中，的`response`输出绑定具有"输出#1"的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

由于输出是 HTTP（仅接受单例值），因此在调用第二次时`Push-OutputBinding`引发错误。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

对于仅接受单例值的输出，可以使用 参数`-Clobber`覆盖旧值，而不是尝试添加到集合中。 下面的示例假定您已经添加了一个值。 通过使用`-Clobber`，以下示例中的响应将覆盖现有值以返回"输出#3"的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>推送输出绑定示例：队列输出绑定

`Push-OutputBinding`用于将数据发送到输出绑定，如[Azure 队列存储输出绑定](functions-bindings-storage-queue-output.md)。 在下面的示例中，写入队列的消息的值是"输出#1"：

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

存储队列的输出绑定接受多个输出值。 在这种情况下，在第一个写入队列后调用以下示例，一个列表具有两个项目："输出#1"和"输出#2"。

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

以下示例在前两个调用后再向输出集合添加两个值：

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

写入队列时，消息包含以下四个值："输出#1"、"输出#2"、"输出#3"和"输出#4"。

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

可以使用`Get-OutputBinding`cmdlet 检索当前为输出绑定设置的值。 此 cmdlet 检索包含输出绑定的名称及其相应值的哈希表。 

以下是用于`Get-OutputBinding`返回当前绑定值的示例：

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`还包含一个参数，`-Name`称为 ，可用于筛选返回的绑定，如以下示例所示：

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

中支持通配符`Get-OutputBinding`（*）

## <a name="logging"></a>Logging

在 PowerShell 中登录功能的工作方式类似于常规 PowerShell 日志记录。 您可以使用日志记录 cmdlet 写入每个输出流。 每个 cmdlet 映射到函数使用的日志级别。

| 函数日志记录级别 | 日志记录 cmdlet |
| ------------- | -------------- |
| 错误 | **`Write-Error`** |
| 警告 | **`Write-Warning`**  | 
| 信息 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 信息 | 写入_信息_级别日志记录。 |
| 调试 | **`Write-Debug`** |
| 跟踪 | **`Write-Progress`** <br /> **`Write-Verbose`** |

除了这些 cmdlet 之外，写入管道的任何内容都重定向到`Information`日志级别，并显示默认的 PowerShell 格式。

> [!IMPORTANT]
> 使用`Write-Verbose`或`Write-Debug`cmdlet 不足以查看详细和调试级别日志记录。 您还必须配置日志级别阈值，该阈值声明您真正关心的日志级别。 要了解更多信息，请参阅[配置函数应用日志级别](#configure-the-function-app-log-level)。

### <a name="configure-the-function-app-log-level"></a>配置功能应用日志级别

Azure 函数允许您定义阈值级别，以便轻松控制函数写入日志的方式。 要设置写入控制台的所有跟踪的阈值，请使用`logging.logLevel.default`[`host.json`文件][host.json 引用]中的属性。 此设置应用于 Function App 中的所有函数。

以下示例设置阈值以为所有函数启用详细日志记录，但设置阈值以启用名为 的`MyFunction`函数的调试日志记录：

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

如果函数应用在 Azure 中运行，则可以使用应用程序见解来监视它。 若要了解有关查看和查询函数日志的详细信息，请阅读[监视 Azure Functions](functions-monitoring.md)。

如果您在本地运行函数应用以进行开发，则默认登录到文件系统。 要查看控制台中的日志，请将其`AZURE_FUNCTIONS_ENVIRONMENT`环境变量设置为启动函数应用`Development`之前。

## <a name="triggers-and-bindings-types"></a>触发器和绑定类型

有许多触发器和绑定可供您用于函数应用。 触发器和绑定的完整列表[可以在此处找到](functions-triggers-bindings.md#supported-bindings)。

所有触发器和绑定在代码中都表示为几个实际数据类型：

* Hashtable
* 字符串
* byte[]
* int
* double
* HttpRequestContext
* Http响应上下文

此列表中的前五种类型是标准 .NET 类型。 最后两个仅由[HttpTrigger 触发器](#http-triggers-and-bindings)使用。

函数中的每个绑定参数都必须是这些类型的一个。

### <a name="http-triggers-and-bindings"></a>HTTP 触发器和绑定

HTTP 和 webhook 触发器以及 HTTP 输出绑定使用请求和响应对象来表示 HTTP 消息。

#### <a name="request-object"></a>请求对象

传递到脚本的请求对象的类型为`HttpRequestContext`，具有以下属性：

| properties  | 描述                                                    | 类型                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 一个包含请求正文的对象。 `Body`根据数据序列化为最佳类型。 例如，如果数据为 JSON，则数据将作为哈希表传入。 如果数据是字符串，则以字符串传入。 | 对象 (object) |
| **`Headers`** | 包含请求标头的字典。                | 字典<字符串，字符串><sup>*</sup> |
| **`Method`** | 请求的 HTTP 方法。                                | 字符串                    |
| **`Params`**  | 一个包含请求的路由参数的对象。 | 字典<字符串，字符串><sup>*</sup> |
| **`Query`** | 一个包含查询参数的对象。                  | 字典<字符串，字符串><sup>*</sup> |
| **`Url`** | 请求的 URL。                                        | 字符串                    |

<sup>*</sup>所有`Dictionary<string,string>`键都不区分大小写。

#### <a name="response-object"></a>响应对象

应发送回的响应对象的类型为`HttpResponseContext`，具有以下属性：

| properties      | 描述                                                 | 类型                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 一个包含响应正文的对象。           | 对象 (object)                    |
| **`ContentType`** | 用于设置响应的内容类型的短手。 | 字符串                    |
| **`Headers`** | 一个包含响应标头的对象。               | 字典或哈希表   |
| **`StatusCode`**  | 响应的 HTTP 状态代码。                       | 字符串或整数             |

#### <a name="accessing-the-request-and-response"></a>访问请求和响应

使用 HTTP 触发器时，可以使用任何其他输入绑定访问 HTTP 请求。 它在街区里`param`

使用`HttpResponseContext`对象返回响应，如下所示：

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

调用此函数的结果是：

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>触发器和绑定的类型转换

对于某些绑定（如 blob 绑定），您可以指定参数的类型。

例如，要将 Blob 存储中的数据作为字符串提供，将以下类型强制转换添加到我的`param`块：

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 配置文件

在 PowerShell 中，有 PowerShell 配置文件的概念。 如果您不熟悉 PowerShell 配置文件，请参阅[关于配置文件](/powershell/module/microsoft.powershell.core/about/about_profiles)。

在 PowerShell 函数中，配置文件脚本在函数应用启动时执行。 函数应用在首次部署时和闲置后启动（[冷启动](#cold-start)）。

使用可视化工作室代码和 Azure 函数核心工具等工具创建函数应用时，将为您创建默认值`profile.ps1`。 默认配置文件维护[在核心工具 GitHub 存储库上](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1)，并包含：

* 自动对 Azure 进行 MSI 身份验证。
* 如果需要，可以打开 Azure PowerShell `AzureRM` PowerShell 别名。

## <a name="powershell-version"></a>PowerShell 版本

下表显示了函数运行时的每个主要版本使用的 PowerShell 版本：

| Functions 版本 | PowerShell 版本                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1（由运行时锁定） |
| 2.x               | 电源外壳核心 6                              |

您可以通过从任何函数打印`$PSVersionTable`来查看当前版本。

## <a name="dependency-management"></a>依赖项管理

函数允许您利用[PowerShell 库](https://www.powershellgallery.com)来管理依赖项。 启用依赖项管理后，要求.psd1 文件用于自动下载所需的模块。 通过将`managedDependency`属性设置为[在 host.json 文件的](functions-host-json.md)根`true`目录中来启用此行为，如以下示例所示：

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

创建新的 PowerShell 函数项目时，默认情况下启用依赖项管理，并附带 Azure[`Az`模块](/powershell/azure/new-azureps-module-az)。 当前支持的最大模块数为 10。 支持的语法是_`MajorNumber`_`.*`或精确的模块版本，如以下要求所示。

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

更新要求.psd1 文件时，重新启动后将安装更新的模块。

> [!NOTE]
> 托管依赖项需要访问www.powershellgallery.com才能下载模块。 在本地运行时，请确保运行时可以通过添加任何必需的防火墙规则访问此 URL。 

以下应用程序设置可用于更改托管依赖项的下载和安装方式。 你的应用升级将在`MDMaxBackgroundUpgradePeriod`中开始，升级过程大约在 内`MDNewSnapshotCheckPeriod`完成。

| 功能应用设置              | 默认值             | 描述                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`（7 天）     | 每个 PowerShell 工作进程都会在进程启动时以及`MDMaxBackgroundUpgradePeriod`之后开始检查 PowerShell 库中的模块升级。 当 PowerShell 库中有新的模块版本时，它已安装到文件系统中，并提供给 PowerShell 工作人员。 减小此值可使函数应用更快地获得较新的模块版本，但也会增加应用资源使用率（网络 I/O、CPU、存储）。 增加此值会降低应用的资源使用量，但也可能延迟向应用交付新的模块版本。 | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`（1 小时）       | 将新的模块版本安装到文件系统后，必须重新启动每个 PowerShell 工作进程。 重新启动 PowerShell 工作人员会影响应用的可用性，因为它可能会中断当前功能执行。 在重新启动所有 PowerShell 工作进程之前，函数调用可以使用旧模块版本或新模块版本。 重新启动在 中`MDNewSnapshotCheckPeriod`完成的所有 PowerShell 工作人员。 增加此值可降低中断的频率，但也可能增加函数调用在非确定性上使用旧模块或新模块版本时的时间段。 |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`（1 天）     | 为了避免频繁重新启动辅助角色时过度升级模块，当任何工作人员已启动上次`MDMinBackgroundUpgradePeriod`签入时，不会执行模块升级检查。 |

利用自己的自定义模块与正常操作的方式略有不同。

在本地计算机上，模块将安装在 中的`$env:PSModulePath`全局可用文件夹中之一。 在 Azure 中运行时，您无法访问计算机上安装的模块。 这意味着`$env:PSModulePath`PowerShell 函数应用与`$env:PSModulePath`常规 PowerShell 脚本不同。

在函数中`PSModulePath`，包含两个路径：

* 功能`Modules`应用根目录处存在的文件夹。
* 由 PowerShell`Modules`语言工作人员控制的文件夹的路径。

### <a name="function-app-level-modules-folder"></a>功能应用级`Modules`文件夹

要使用自定义模块，可以将函数所依赖的模块放在`Modules`文件夹中。 在此文件夹中，模块自动可用于函数运行时。 函数应用中的任何函数都可以使用这些模块。 

> [!NOTE]
> 在要求.psd1 文件中指定的模块会自动下载并包含在路径中，因此您无需将它们包含在模块文件夹中。 当在云中运行时，`$env:LOCALAPPDATA/AzureFunctions``/data/ManagedDependencies`这些存储在本地文件夹中和文件夹中。

要利用自定义模块功能，请在函数应用的`Modules`根目录中创建一个文件夹。 将函数中要使用的模块复制到此位置。

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

使用`Modules`文件夹时，函数应用应具有以下文件夹结构：

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

启动函数应用时，PowerShell 语言工作人员会将此`Modules`文件夹添加到 中`$env:PSModulePath`，以便您可以像在常规 PowerShell 脚本中一样依赖模块自动加载。

### <a name="language-worker-level-modules-folder"></a>语言工作者级别`Modules`文件夹

PowerShell 语言工作人员通常使用多个模块。 这些模块是在 的最后位置`PSModulePath`定义的。 

当前模块列表如下：

* [微软.PowerShell.存档](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive)：用于处理存档的模块，如`.zip`，`.nupkg`等。
* **线程作业**：基于线程的 PowerShell 作业 API 的实现。

默认情况下，函数使用这些模块的最新版本。 要使用特定的模块版本，请将该特定版本放在函数`Modules`应用的文件夹中。

## <a name="environment-variables"></a>环境变量

在 Functions 中，服务连接字符串等[应用设置](functions-app-settings.md)在执行过程中将公开为环境变量。 您可以使用 访问这些设置，`$env:NAME_OF_ENV_VAR`如以下示例所示：

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在本地运行时，可从 [local.settings.json](functions-run-local.md#local-settings-file) 项目文件读取应用设置。

## <a name="concurrency"></a>并发

默认情况下，函数 PowerShell 运行时一次只能处理一个函数的调用。 但是，在以下情况下，此并发级别可能不够：

* 当您尝试同时处理大量调用时。
* 当您有调用同一函数应用内其他函数的函数时。

通过将以下环境变量设置为整数值，可以更改此行为：

```
PSWorkerInProcConcurrencyUpperBound
```

在函数应用[的应用设置](functions-app-settings.md)中设置此环境变量。

### <a name="considerations-for-using-concurrency"></a>使用并发的注意事项

默认情况下，PowerShell 是_一种线程_脚本语言。 但是，可以通过在同一进程中使用多个 PowerShell 运行空间来添加并发。 创建的运行空间量将与 PSWorkerInConcConcConinins 上绑定应用程序设置匹配。 吞吐量将受到所选计划中可用的 CPU 和内存量的影响。

Azure PowerShell 使用某些_进程级_上下文和状态来帮助您避免过度键入。 但是，如果在函数应用中打开并发性并调用更改状态的操作，则最终可能会使用争用条件。 这些争用条件很难调试，因为一个调用依赖于特定状态，另一个调用更改了状态。

与 Azure PowerShell 并发性具有巨大的价值，因为某些操作可能需要相当长的时间。 但是，您必须谨慎行事。 如果您怀疑您遇到争用条件，将 PSWorkerInConcConininsUpperBound 应用设置设置为`1`，而是使用[语言辅助进程级别隔离](functions-app-settings.md#functions_worker_process_count)进行并发。

## <a name="configure-function-scriptfile"></a>配置功能`scriptFile`

默认情况下，PowerShell 函数从`run.ps1`执行，该文件与其对应`function.json`的共享同一父目录。

中的`scriptFile``function.json`属性可用于获取如下所示的文件夹结构：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

在这种情况下， for`function.json``myFunction`包括一个`scriptFile`属性，该属性引用要运行的导出函数的文件。

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>通过配置入口点使用 PowerShell 模块

本文在模板生成的默认`run.ps1`脚本文件中显示了 PowerShell 函数。
但是，您还可以在 PowerShell 模块中包括您的功能。 您可以使用 函数.json 的配置文件中的`scriptFile`和`entryPoint`字段来引用模块中的特定函数代码。

在这种情况下，`entryPoint`是 中`scriptFile`引用的 PowerShell 模块中的函数或 cmdlet 的名称。

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

在此示例中，的`myFunction`配置包括引用`scriptFile``PSFunction.psm1`的属性，该属性是另一个文件夹中的 PowerShell 模块。  属性`entryPoint`引用函数`Invoke-PSTestFunc`，该函数是模块中的入口点。

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

使用此配置，`Invoke-PSTestFunc`将完全按照 将`run.ps1`执行。

## <a name="considerations-for-powershell-functions"></a>PowerShell 功能注意事项

使用 PowerShell 函数时，请注意以下各节中的注意事项。

### <a name="cold-start"></a>冷启动

在[无服务器托管模型中](functions-scale.md#consumption-plan)开发 Azure 函数时，冷启动是一个现实。 *冷启动*是指函数应用开始运行以处理请求所需的时间。 冷启动在消耗计划中更频繁地发生，因为函数应用在非活动期间关闭。

### <a name="bundle-modules-instead-of-using-install-module"></a>捆绑模块，而不是使用`Install-Module`

每次调用时都会运行脚本。 避免`Install-Module`在脚本中使用。 而是在发布`Save-Module`之前使用，这样您的功能就不必浪费时间下载模块。 如果冷启动会影响您的功能，请考虑将函数应用部署到设置为*始终打开*或部署到[高级计划](functions-scale.md#premium-plan)[的应用服务](functions-scale.md#app-service-plan)计划。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [Azure Functions 最佳做法](functions-best-practices.md)
* [Azure Functions developer reference（Azure Functions 开发人员参考）](functions-reference.md)
* [Azure 函数触发器和绑定](functions-triggers-bindings.md)

[host.json 引用]: functions-host-json.md
