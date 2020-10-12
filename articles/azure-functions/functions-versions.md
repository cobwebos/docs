---
title: Azure Functions 运行时版本概述
description: Azure Functions 支持多个版本的运行时。 了解这些版本之间的差异以及如何选择最适合你的版本。
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 12/09/2019
ms.openlocfilehash: 6641461e63d7c9452351f3b0b99a274d2714a92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88208106"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 运行时版本概述

Azure Functions 当前支持三个版本的运行时主机：1.x、2.x 和 3.x。 生产方案支持所有三个版本。  

> [!IMPORTANT]
> 版本 1.x 处于维护模式，仅支持在 Azure 门户或本地 Windows 计算机上进行开发。 仅在更高版本中提供增强功能。 

本文详细介绍了不同版本之间的一些差异、如何创建每个版本，以及如何更改版本。

## <a name="languages"></a>语言

从版本 2.x 开始，运行时使用语言扩展性模型，并且函数应用中的所有函数必须共享同一语言。 函数应用中的函数语言是在创建应用时选择的，并且在 [FUNCTIONS\_WORKER\_RUNTIME](functions-app-settings.md#functions_worker_runtime) 设置中进行维护。 

下表指示每个运行时版本目前支持的编程语言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

有关详细信息，请参阅[支持的语言](supported-languages.md)。

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>在特定版本上运行

默认情况下，在 Azure 门户中和通过 Azure CLI 创建的函数应用将设置为版本 3.x。 你可以根据需要修改此版本。 只能在创建函数应用之后、添加任何函数之前将运行时版本更改为 1.x。  即使应用具有函数，也允许在 2.x 和 3.x 之间迁移，但仍建议先在新应用中进行测试。

## <a name="migrating-from-1x-to-later-versions"></a>从 1.x 迁移到更高版本

可以选择迁移所编写的现有应用，以使用 1.x 版运行时，而不使用较新版本。 需要做出的大多数更改与语言运行时的更改相关，例如，.NET Framework 4.7 与 .NET Core 之间的 C# API 更改。 还需要确保代码和库与所选的语言运行时兼容。 最后，请务必注意触发器、绑定和以下突出显示功能中的任何更改。 为获得最佳迁移结果，应当在新版本中创建一个新的函数应用，并将现有的 1.x 版函数代码移植到新应用。  

尽管可以通过手动更新应用配置来执行“就地”升级，但从 1.x 转变到更高版本包括了一些中断性变更。 例如，在 C# 中，调试对象从 `TraceWriter` 更改为 `ILogger`。 创建新的 3.x 版项目后，可以基于最新的 3.x 版模板，从更新的函数着手进行开发。

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>1\.x 之后版本中触发器和绑定的更改

从版本 2.x 开始，必须为应用中的函数所用的特定触发器和绑定安装扩展。 唯一的例外是 HTTP 和计时器触发器，它们不需要扩展。  有关详细信息，请参阅[注册和安装绑定扩展](./functions-bindings-register.md)。

此外，在不同的版本中，函数的 *function.json* 或属性存在几处更改。 例如，事件中心的 `path` 属性现在为 `eventHubName`。 请参阅[现有绑定表](#bindings)，以获取每个绑定的文档链接。

### <a name="changes-in-features-and-functionality-after-version-1x"></a>1\.x 之后版本中特性和功能的更改

在版本 1.x 后删除、更新或替换了几个特性。 本部分详细介绍了在使用版本 1.x 后，更高版本中会出现的更改。

在版本 2.x 中做出了以下更改：

* 用于调用 HTTP 终结点的密钥始终以加密方式存储在 Azure Blob 存储中。 在版本 1.x 中，密钥将默认存储在 Azure 文件存储中。 将应用从版本 1.x 升级到版本 2.x 时，会重置文件存储中的现有机密。

* 2\.x 版运行时不包含对 Webhook 提供程序的内置支持。 做出此项更改的目的是提高性能。 仍可以使用 HTTP 触发器作为 Webhook 的终结点。

* 主机配置文件 (host.json) 应该为空或包含字符串 `"version": "2.0"`。

* 为了改进监视功能，门户中使用 [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) 设置的 WebJobs 仪表板已替换为使用 [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) 设置的 Azure Application Insights。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

* 函数应用中的所有函数必须共享相同的语言。 创建函数应用时，必须选择该应用的运行时堆栈。 运行时堆栈由应用程序设置中的 [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 值指定。 增加此项要求的目的是减少占用空间和启动时间。 进行本地开发时，还必须在 [local.settings.json 文件](functions-run-local.md#local-settings-file)中包含此设置。

* 应用服务计划中函数的默认超时已更改为 30 分钟。 可以使用 host.json 中的 [functionTimeout](functions-host-json.md#functiontimeout) 设置，将超时手动改回到无限。

* 默认情况下，将对消耗计划函数实施 HTTP 并发性限制，每个实例的并发请求数默认为 100。 可以在 host.json 文件中的 [`maxConcurrentRequests`](functions-host-json.md#http) 设置内更改此值。

* 由于 [.NET Core 的限制](https://github.com/Azure/azure-functions-host/issues/3414)，已删除对 F# 脚本 (.fsx) 函数的支持。 编译的 F# 函数 (.fs) 仍受支持。

* 事件网格触发器 Webhook 的 URL 格式已更改为 `https://{app}/runtime/webhooks/{triggerName}`。

## <a name="migrating-from-2x-to-3x"></a>从 2.x 迁移到 3.x

Azure Functions 版本 3.x 向后高度兼容版本 2.x。  许多应用应该能够安全地升级到 3.x，无需更改任何代码。  虽然建议迁移到 3.x，但在生产应用的主版本中进行更改之前，务必运行大量测试。

### <a name="breaking-changes-between-2x-and-3x"></a>2\.x 和 3.x 之间的中断性变更

下面是在将 2.x 应用升级到 3.x 之前要注意的变更。

#### <a name="javascript"></a>Javascript

* 通过 `context.done` 或返回值分配的输出绑定现在与在 `context.bindings` 中进行设置具有相同的行为。

* 计时器触发器对象采用 camelCase 而非 PascalCase

* `dataType` 为 binary 的事件中心触发函数将收到 `binary` 数组而非 `string` 数组。

* 无法再通过 `context.bindingData.req` 访问 HTTP 请求有效负载。  仍然可以在 `context.bindings` 中以输入参数 `context.req` 的形式对其进行访问。

* Node.js 8 不再受支持，并且在 3.x 函数中不会执行。

#### <a name="net"></a>.NET

* [默认情况下会禁用同步服务器操作](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers)。

### <a name="changing-version-of-apps-in-azure"></a>在 Azure 中更改应用版本

Azure 中的已发布应用使用的 Functions 运行时版本由 [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) 应用程序设置指定。 支持以下主要运行时版本值：

| Value | 运行时目标 |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> 请不要随意更改此设置，因为这可能需要更改其他应用设置以及函数代码。

### <a name="locally-developed-application-versions"></a>本地开发的应用程序版本

你可以对函数应用进行以下更新以在本地更改目标版本。

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 运行时版本

在 Visual Studio 中，可在创建项目时选择运行时版本。 用于 Visual Studio 的 Azure Functions 工具支持这三个主要运行时版本。 基于项目设置进行调试和发布时，将使用正确的版本。 版本设置在 `.csproj` 文件中的以下属性内定义：

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
> Azure Functions 3.x 和 .NET 要求 `Microsoft.NET.Sdk.Functions` 扩展至少为 `3.0.0`。

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>在 Visual Studio 中将 2.x 应用更新到 3.x

你可以打开面向 2.x 的现有函数，并通过编辑 `.csproj` 文件并更新上述值来迁移到 3.x。  Visual Studio 将基于项目元数据自动管理运行时版本。  但是，如果你之前从未创建过 3.x 应用，则 Visual Studio 在你的计算机上可能尚无适用于 3.x 的模板和运行时。  这可能会出现错误，例如“没有与项目中指定的版本匹配的可用函数运行时”  若要提取最新的模板和运行时，请完成体验来创建新的函数项目。  到达版本和模板选择屏幕后，请等待 Visual Studio 完成最新模板提取。  在最新的 .NET Core 3 模板可用并显示后，你应该能够运行和调试为版本 3.x 配置的任何项目。

> [!IMPORTANT]
> 只有使用 Visual Studio 版本 16.4 或更高版本时，才能在 Visual Studio 中开发 3.x 版函数。

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 和 Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) 可用于命令行开发，另外，还可供适用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)使用。 若要针对版本 3.x 进行开发，请安装 Core Tools 版本 3.x。 版本 2.x 开发需要 Core Tools 版本 2.x，依此类推。 有关详细信息，请参阅[安装 Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)。

对于 Visual Studio Code 开发，可能还需要更新 `azureFunctions.projectRuntime` 的用户设置，使之与已安装工具的版本匹配。  此设置还会更新创建函数应用期间使用的模板和语言。  若要在 `~3` 中创建应用，请将 `azureFunctions.projectRuntime` 用户设置更新为 `~3`。

![Azure Functions 扩展运行时设置](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven 和 Java 应用

通过[安装在本地运行所需的 Core Tools 3.x 版本](functions-run-local.md#install-the-azure-functions-core-tools)，可以将 Java 应用从版本 2.x 迁移到 3.x。  验证你的应用在 3.x 版中本地运行并正常工作后，更新应用的 `POM.xml` 文件来将 `FUNCTIONS_EXTENSION_VERSION` 设置修改为 `~3`，如以下示例中所示：

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

从版本 2.x 开始，运行时使用新的[绑定扩展性模型](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)，该模型具有以下优势：

* 支持第三方绑定扩展。

* 运行时和绑定分离。 此项更改允许对绑定扩展进行版本控制和单独发布操作。 例如，可以选择升级到依赖于基础 SDK 的较新版本的扩展版本。

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
