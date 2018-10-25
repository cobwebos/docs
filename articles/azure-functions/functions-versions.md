---
title: Azure Functions 运行时版本概述
description: Azure Functions 支持多个版本的运行时。 了解这些版本之间的差异以及如何选择最适合你的版本。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: f2f1313461fcb58ea48af99aeda2f7005534fe34
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885181"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 运行时版本概述

 Azure Functions 运行时有两个主版本：1.x 和 2.x。 当前版本为 2.x，其中可使用新功能并在进行改进，但两个版本都支持生产方案。  下面详细介绍了两个版本的一些区别、如何创建每个版本以及从 1.x 升级到 2.x。

> [!NOTE]
> 本文引用了云服务 Azure Functions。 如需深入了解可运行本地 Azure Functions 的预览产品，请参阅 [Azure Functions 运行时概述](functions-runtime-overview.md)。

## <a name="cross-platform-development"></a>跨平台开发

2.x 版运行时在 .NET Core 2 上运行，因此，它可以在 .NET Core 支持的所有平台（包括 macOS 和 Linux）上运行。 在 .NET Core 上运行可以实现跨平台开发和托管方案。

相比之下，1.x 版运行时仅支持 Azure 门户或 Windows 计算机上的开发和托管。

## <a name="languages"></a>语言

2.x 版运行时使用新的语言扩展性模型。 在版本 2.x 中，函数应用中的所有函数必须共享相同的语言。 函数应用中的函数语言是在创建应用时选择的。

Azure Functions 1.x 试验性语言不会更新为使用新模型，因此它们在 2.x 中不受支持。 下表指示每个运行时版本目前支持的编程语言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

有关详细信息，请参阅[支持的语言](supported-languages.md)。

## <a name="creating-1x-apps"></a>在版本 1.x 上运行

默认情况下，在 Azure 门户中创建的函数应用设置为版本 2.x。 应尽可能地使用此运行时版本，因为其中包含投资开发的新功能。 如果需要，你仍可以在版本 1.x 运行时中运行函数应用。 只能在创建函数应用之后、添加任何函数之前更改运行时版本。 若要了解如何将运行时版本固定为 1.x，请参阅[查看和更新当前运行时版本](set-runtime-version.md#view-and-update-the-current-runtime-version)。

## <a name="migrating-from-1x-to-2x"></a>从 1.x 迁移到 2.x

可以选择迁移所编写的现有应用，以使用 1.x 版运行时，而不使用版本 2.x。 需要做出的大多数更改与语言运行时的更改相关，例如，.NET Framework 4.7 与 .NET Core 2 之间的 C# API 更改。 还需要确保代码和库与所选的语言运行时兼容。 最后，请务必注意触发器、绑定和以下突出显示功能中的任何更改。 为获得最佳迁移结果，应该为版本 2.x 创建一个新函数应用，并将现有的 1.x 版函数代码移植到新应用。  

### <a name="changes-in-triggers-and-bindings"></a>触发器和绑定的更改

版本 2.x 要求为应用中的函数所用的特定触发器和绑定安装扩展。 唯一的例外是 HTTP 和计时器触发器，它们不需要扩展。  有关详细信息，请参阅[注册和安装绑定扩展](./functions-triggers-bindings.md#register-binding-extensions)。

此外，在不同的版本中，函数的 `function.json` 或属性存在几处更改。 例如，事件中心的 `path` 属性现在为 `eventHubName`。 请参阅[现有绑定表](#bindings)，以获取每个绑定的文档链接。

### <a name="changes-in-features-and-functionality"></a>特性和功能的更改

此外，新版本中已删除、更新或替换了几项功能。 本部分详细介绍在使用版本 1.x 后，版本 2.x 中会出现的更改。

在版本 2.x 中做出了以下更改：

* 用于调用 HTTP 终结点的密钥始终以加密方式存储在 Azure Blob 存储中。 在版本 1.x 中，密钥默认存储在 Azure 文件存储中。 将应用从版本 1.x 升级到版本 2.x 时，将重置文件存储中的现有机密。

* 2.x 版运行时不包含对 Webhook 提供程序的内置支持。 做出此项更改的目的是提高性能。 仍可以使用 HTTP 触发器作为 Webhook 的终结点。

* 主机配置文件 (host.json) 应该为空或包含字符串 `"version": "2.0"`。

* 为了改进监视功能，门户中使用 [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) 设置的 WebJobs 仪表板已替换为使用 [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsightsinstrumentationkey) 设置的 Azure Application Insights。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

* 函数应用中的所有函数必须共享相同的语言。 创建函数应用时，必须选择该应用的运行时堆栈。 运行时堆栈由应用程序设置中的 [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functionsworkerruntime) 值指定。 增加此项要求的目的是减少占用空间和启动时间。 进行本地开发时，还必须在 [local.settings.json 文件](functions-run-local.md#local-settings-file)中包含此设置。

* 应用服务计划中函数的默认超时已更改为 30 分钟。 可以使用 host.json 中的 [functionTimeout](functions-host-json.md#functiontimeout) 设置，将超时手动改回到 unlimited（无限）。

* 默认情况下，将对消耗计划函数实施 HTTP 并发性限制，每个实例的并发请求数默认为 100。 可以在 host.json 文件中的 [`maxConcurrentRequests`](functions-host-json.md#http) 设置内更改此值。

* 由于 [.NET Core 的限制](https://github.com/Azure/azure-functions-host/issues/3414)，已删除对 F# 脚本 (.fsx) 函数的支持。 编译的 F# 函数 (.fs) 仍受支持。

* 事件网格触发器 Webhook 的 URL 格式已更改为 `https://{app}/runtime/webhooks/{triggerName}`。

### <a name="migrating-a-locally-developed-application"></a>迁移本地开发的应用程序

现有的函数应用项目可能是使用 1.x 版运行时在本地开发的。 若要升级到版本 2.x，应该针对版本 2.x 创建一个本地函数应用项目，并将现有代码移植到新应用。 可以手动更新现有的项目和代码，这是一种“就地”升级的方法。 但是，在版本 1.x 与版本 2.x 之间，可能还需要做出其他一些改进。 例如，在 C# 中，调试对象已从 `TraceWriter` 更改为 `ILogger`。 创建新的 2.x 版项目后，可以基于最新的 2.x 版模板，从更新的函数着手进行开发。

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 运行时版本

在 Visual Studio 中，可在创建项目时选择运行时版本。 用于 Visual Studio 的 Azure Functions 工具支持这两个主要运行时版本。 基于项目设置进行调试和发布时，将使用正确的版本。 版本设置在 `.csproj` 文件中的以下属性内定义：

##### <a name="version-1x"></a>版本 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>版本 2.x

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

调试或发布项目时，将使用正确的运行时版本。

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 和 Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) 可用于命令行开发，另外，还可供用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)使用。 若要针对版本 2.x 进行开发，请安装 Core Tools 版本 2.x。 版本 1.x 开发需要 Core Tools 版本 1.x。 有关详细信息，请参阅[安装 Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)。

对于 Visual Studio Code 开发，可能还需要更新 `azureFunctions.projectRuntime` 的用户设置，以便与安装的工具版本匹配。  此设置还会更新创建函数应用期间使用的模板和语言。

### <a name="changing-version-of-apps-in-azure"></a>在 Azure 中更改应用版本

Azure 中已发布的应用使用的 Functions 运行时版本由 [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functionsextensionversion) 应用程序设置指定。 值 `~2` 表示面向 2.x 版运行时，`~1` 表示面向 1.x 版运行时。 请不要随意更改此设置，因为这可能需要在函数中进行其他应用设置更改和代码更改。 若要了解将函数应用迁移到不同运行时版本的建议方法，请参阅[如何以 Azure Functions 运行时版本为目标](set-runtime-version.md)。

## <a name="bindings"></a>绑定

2.x 版运行时使用新的[绑定扩展性模型](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)，该模型提供以下优势：

* 支持第三方绑定扩展。

* 运行时和绑定分离。 此项更改允许对绑定扩展进行版本控制和单独发布。 例如，可以选择升级到依赖于基础 SDK 的较新版本的扩展版本。

* 更轻便的执行环境，其中运行时仅知道和加载正在使用的绑定。

除 HTTP 和计时器触发器外，其他所有绑定必须显式添加到函数应用项目，或者在门户中注册。 有关详细信息，请参阅[注册绑定扩展](functions-triggers-bindings.md#register-binding-extensions)。

下表显示了每个运行时版本支持的绑定。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [在本地对 Azure Functions 进行编码和测试](functions-run-local.md)
* [如何面向 Azure Functions 运行时版本](set-runtime-version.md)
* [发行说明](https://github.com/Azure/azure-functions-host/releases)
