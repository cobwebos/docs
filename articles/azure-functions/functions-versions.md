---
title: Azure Functions 运行时版本概述
description: Azure Functions 支持多个版本的运行时。 了解这些版本之间的差异以及如何选择最适合你的版本。
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0138af15c89a4792b9ccb73a8b761577fad36c5e
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290370"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 运行时版本概述

Azure Functions 运行时的主版本与运行时所基于的 .NET 版本相关。 下表指明了运行时的当前版本、发布级别以及相关的 .NET 版本。 

| 运行时版本 | 版本级别<sup>1</sup> | .NET 版本 | 
| --------------- | ------------- | ------------ |
| 3.x | GA | .NET Core 3.1 | 
| 2.x | GA | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4.6<sup>3</sup> |

<sup>1</sup>生产方案支持 GA 版本。   
<sup>2</sup>版本1.x 处于维护模式。 仅在更高版本中提供增强功能。   
<sup>3</sup>仅支持在 Azure 门户或本地 Windows 计算机上进行开发。

本文详细介绍了不同版本之间的一些差异，如何创建每个版本，以及如何更改版本。

## <a name="languages"></a>语言

从版本2.x 开始，运行时使用语言扩展性模型，函数应用中的所有函数必须共享同一语言。 函数应用中的函数语言是在创建应用时选择的，并且在[\_WORKER\_运行时](functions-app-settings.md#functions_worker_runtime)设置的函数中维护。 

Azure Functions 1.x 的实验性语言不能使用新模型，因此在2.x 中不受支持。 下表指示每个运行时版本目前支持的编程语言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

有关详细信息，请参阅[支持的语言](supported-languages.md)。

## <a name="creating-1x-apps"></a>在特定版本上运行

默认情况下，在 Azure 门户和 Azure CLI 中创建的函数应用被设置为版本2.x。 您可以根据需要修改此版本。 你只能在创建 function app 之后但在添加任何函数之前将运行时版本更改为1.x。  即使应用具有函数，也允许在1.x 和2.x 之间移动，但仍建议先在新应用程序中进行测试。

## <a name="migrating-from-1x-to-later-versions"></a>从1.x 迁移到更高版本

你可以选择将编写的现有应用程序迁移为使用版本1.x 运行时，而不是使用较新的版本。 你需要做的大多数更改都与语言运行时中的更改有关，例如 .NET Framework 4.7 C#与 .net Core 之间的 API 更改。 还需要确保代码和库与所选的语言运行时兼容。 最后，请务必注意触发器、绑定和以下突出显示功能中的任何更改。 为获得最佳迁移结果，应在新版本中创建新的 function app，并将现有版本1.x 函数代码移植到新应用。  

尽管可以通过手动更新应用程序配置来执行 "就地" 升级，但从1.x 到更高版本，包括一些重大更改。 例如，在中C#，调试对象从 `TraceWriter` 更改为 `ILogger`。 通过创建新的版本1.x 项目，你可以根据最新的版本1.x 模板开始使用更新的函数。

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>版本1.x 后的触发器和绑定中的更改

从版本2.x 开始，必须为应用中的函数使用的特定触发器和绑定安装扩展。 唯一的例外是 HTTP 和计时器触发器，它们不需要扩展。  有关详细信息，请参阅[注册和安装绑定扩展](./functions-bindings-register.md)。

*函数的 json*或版本之间的函数的属性也有一些更改。 例如，事件中心的 `path` 属性现在为 `eventHubName`。 请参阅[现有绑定表](#bindings)，以获取每个绑定的文档链接。

### <a name="changes-in-features-and-functionality-after-version-1x"></a>版本1.x 后的特性和功能更改

在版本1.x 后删除、更新或替换了几个功能。 本部分详细介绍了使用版本1.x 后在更高版本中看到的更改。

在版本 2.x 中做出了以下更改：

* 用于调用 HTTP 终结点的密钥始终以加密方式存储在 Azure Blob 存储中。 在版本 1.x 中，密钥默认存储在 Azure 文件存储中。 将应用从版本 1.x 升级到版本 2.x 时，将重置文件存储中的现有机密。

* 2\.x 版运行时不包含对 Webhook 提供程序的内置支持。 做出此项更改的目的是提高性能。 仍可以使用 HTTP 触发器作为 Webhook 的终结点。

* 主机配置文件 (host.json) 应该为空或包含字符串 `"version": "2.0"`。

* 为了改进监视功能，门户中使用 [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) 设置的 WebJobs 仪表板已替换为使用 [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) 设置的 Azure Application Insights。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

* 函数应用中的所有函数必须共享相同的语言。 创建函数应用时，必须选择该应用的运行时堆栈。 运行时堆栈由应用程序设置中的 [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 值指定。 增加此项要求的目的是减少占用空间和启动时间。 进行本地开发时，还必须在 [local.settings.json 文件](functions-run-local.md#local-settings-file)中包含此设置。

* 应用服务计划中函数的默认超时已更改为 30 分钟。 可以使用 host.json 中的 [functionTimeout](functions-host-json.md#functiontimeout) 设置，将超时手动改回到 unlimited（无限）。

* 默认情况下，为消耗计划函数实现 HTTP 并发限制，默认为每个实例100个并发请求。 可以在 host.json 文件中的 [`maxConcurrentRequests`](functions-host-json.md#http) 设置内更改此值。

* 由于[.Net Core 限制](https://github.com/Azure/azure-functions-host/issues/3414)，已删除对F#脚本（. .fsx）函数的支持。 编译的 F# 函数 (.fs) 仍受支持。

* 事件网格触发器 Webhook 的 URL 格式已更改为 `https://{app}/runtime/webhooks/{triggerName}`。

## <a name="migrating-from-2x-to-3x"></a>从1.x 迁移到2。x

版本 3.x Azure Functions 与版本2.x 高度向后兼容。  许多应用程序应该能够安全地升级到3.x，无需进行任何代码更改。  在迁移到3.x 时，请确保在更改生产应用的主要版本之前运行大量测试。

### <a name="breaking-changes-between-2x-and-3x"></a>1\.x 和2.x 之间的重大更改

下面是在将2.x 应用升级到2.x 之前要注意的更改。

#### <a name="javascript"></a>JavaScript

* 通过 `context.done` 或返回值分配的输出绑定现在与 `context.bindings`中的设置相同。

* Timer 触发器对象是 camelCase 而不是 PascalCase

* 事件中心使用 `dataType` 二进制文件触发的函数将接收 `binary` 的数组，而不是 `string`。

* 无法再通过 `context.bindingData.req`访问 HTTP 请求负载。  它仍可作为输入参数 `context.req`，并在 `context.bindings`中进行访问。

* Node.js 8 不再受支持，并且不会在1.x 函数中执行。

#### <a name="net"></a>.NET

* [同步服务器操作默认情况下处于禁用状态](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers)。

### <a name="changing-version-of-apps-in-azure"></a>在 Azure 中更改应用版本

Azure 中已发布的应用使用的 Functions 运行时版本由 [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) 应用程序设置指定。 支持以下主要运行时版本值：

| 值 | 运行时目标 |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> 不要随意更改此设置，因为其他应用程序设置也可能需要更改和函数代码的更改。

### <a name="locally-developed-application-versions"></a>本地开发的应用程序版本

你可以进行以下更新来使函数应用在本地更改目标版本。

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 运行时版本

在 Visual Studio 中，可在创建项目时选择运行时版本。 适用于 Visual Studio Azure Functions 工具支持三种主要的运行时版本。 基于项目设置进行调试和发布时，将使用正确的版本。 版本设置在 `.csproj` 文件中的以下属性内定义：

##### <a name="version-1x"></a>版本 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>版本 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>3\.x 版

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 1.x 和 .NET 至少需要 `Microsoft.NET.Sdk.Functions` 扩展 `3.0.0`。

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>在 Visual Studio 中将2.x 应用更新到3。x

您可以通过编辑 `.csproj` 文件并更新上述值，打开面向2.x 的现有函数并转到3.x。  Visual Studio 将基于项目的元数据自动管理运行时版本。  不过，如果你从未创建了一个1.x 应用程序，但你的计算机上尚不具有用于1.x 的模板和运行时，则可能会出现这种情况。  这可能会出现错误，如 "没有与项目中指定的版本相匹配的函数运行时"。  若要提取最新的模板和运行时，请完成创建新函数项目的经验。  转到版本和模板选择屏幕后，请等待 Visual Studio 完成提取最新模板。  最新的 .NET Core 3 模板可用并显示后，您应该能够运行和调试为版本2.x 配置的任何项目。

> [!IMPORTANT]
> 仅当使用 Visual Studio 版本16.4 或更高版本时，才能在 Visual Studio 中开发版本1.x 函数。

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 和 Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) 可用于命令行开发，另外，还可供用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)使用。 若要针对版本2.x 进行开发，请安装 Core Tools 的版本2.x。 版本2.x 开发需要版本2.x 的核心工具，等等。 有关详细信息，请参阅[安装 Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)。

对于 Visual Studio Code 开发，可能还需要更新 `azureFunctions.projectRuntime` 的用户设置，以便与安装的工具版本匹配。  此设置还会更新创建函数应用期间使用的模板和语言。  若要在 `~3` 中创建应用，请将 `azureFunctions.projectRuntime` 用户设置更新为 "`~3`"。

![Azure Functions 扩展运行时设置](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven 和 Java 应用

可以通过安装本地运行所需的[core 工具](functions-run-local.md#install-the-azure-functions-core-tools)的2.x 版，将 Java 应用从版本2.x 迁移到2.x 版。  在验证应用是否正常运行于版本2.x 的本地运行后，更新应用的 `POM.xml` 文件，将 `FUNCTIONS_EXTENSION_VERSION` 设置修改为 `~3`，如以下示例中所示：

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>绑定

从版本2.x 开始，运行时使用新的[绑定扩展性模型](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)，该模型具有以下优势：

* 支持第三方绑定扩展。

* 运行时和绑定分离。 此项更改允许对绑定扩展进行版本控制和单独发布。 例如，可以选择升级到依赖于基础 SDK 的较新版本的扩展版本。

* 更轻便的执行环境，其中运行时仅知道和加载正在使用的绑定。

除 HTTP 和计时器触发器外，其他所有绑定必须显式添加到函数应用项目，或者在门户中注册。 有关详细信息，请参阅[注册绑定扩展](./functions-bindings-expressions-patterns.md)。

下表显示了每个运行时版本支持的绑定。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [在本地对 Azure Functions 进行编码和测试](functions-run-local.md)
* [如何面向 Azure Functions 运行时版本](set-runtime-version.md)
* [发行说明](https://github.com/Azure/azure-functions-host/releases)
