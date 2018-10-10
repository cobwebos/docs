---
title: Azure Functions 运行时版本概述
description: Azure Functions 支持多个版本的运行时。 了解这些版本之间的差异以及如何选择最适合你的版本。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: a601ea42549abad84d6cab5c429cf94147776436
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978618"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 运行时版本概述

 Azure Functions 运行时有两个主版本：1.x 和 2.x。 当前版本为 2.x，其中可使用新功能并在进行改进，但两个版本都支持生产方案。  下面详细介绍了两个版本的一些区别、如何创建每个版本以及从 1.x 升级到 2.x。

> [!NOTE] 
> 本文引用了云服务 Azure Functions。 如需深入了解可运行本地 Azure Functions 的预览产品，请参阅 [Azure Functions 运行时概述](functions-runtime-overview.md)。

## <a name="creating-1x-apps"></a>创建 1.x 应用

默认情况下，Azure 门户中创建的新应用设置为 2.x，因为这是最新版本，并且正在进行新功能投资。  但是，仍可以通过执行以下操作创建 v1.x 应用。

1. 从 Azure 门户中创建一个 Azure 函数
1. 打开创建的应用，应用为空白时，打开“函数设置”
1. 将版本从 ~2 更改为 ~1。  如果应用中含有函数，则将禁用此切换。
1. 单击“保存”并重启该应用。  现在，所有模板应在 1.x 中创建和运行。

## <a name="cross-platform-development"></a>跨平台开发

运行时 1.x 仅支持在门户中或在 Windows 上进行开发和托管。 运行时 2.x 在 .NET Core 2 上运行，这意味着它可以在 .NET Core 支持的所有平台上运行，包括 macOS 和 Linux。 这可实现跨平台开发和托管方案。

## <a name="languages"></a>语言

运行时 2.x 使用新的语言扩展性模型。 此外，为改进工具和提升性能，2.x 中的每个应用仅限于使用一种语言的函数。 2.x 中当前支持的语言有 C#、F#、JavaScript 和 Java。 Azure Functions 1.x 实验性语言尚未更新为使用此新模型，因此它们在 2.x 中不受支持。 下表指示每个运行时版本支持的编程语言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

有关详细信息，请参阅[支持的语言](supported-languages.md)。

## <a name="migrating-from-1x-to-2x"></a>从 1.x 迁移到 2.x

你可能希望将在 1.x 中编写的现有应用移动到 2.x。  在版本之间移动，需要考虑的大多数注意事项均与上面列出的语言运行时更改有关（例如，C# 从 .NET Framework 4.7 移动到 .NET Core 2）。  请确保代码和库与使用的语言运行时兼容。  此外，请务必注意触发器、绑定和以下突出显示功能中的任何更改。

### <a name="changes-in-triggers-and-bindings"></a>触发器和绑定的更改

虽然大多数触发器和绑定属性和配置在不同版本之间保持不变，但 2.x 中需要向应用安装任何触发器或绑定。 唯一的例外是 HTTP 和计时器触发器。  请参阅[注册和安装绑定扩展](./functions-triggers-bindings.md#register-binding-extensions)。  请注意，不同版本之间函数的 `function.json` 或属性也可能发生更改（例如，CosmosDB `connection` 属性现在为 `ConnectionStringSetting`）。  引用[现有绑定表](#bindings)，以获取每个绑定的文档链接。

### <a name="changes-in-features-available"></a>可用功能的更改

不同版本之间除了语言和绑定的更改，还删除、更新或替换了一些功能。  以下是使用 1.x 后开始使用 2.x 时一些主要注意事项。  v2.x 中进行了以下更改：

* 调用函数的密钥将始终存储在加密的 blob 存储中。 在 1.x 中，默认情况下它们位于文件存储中，如果启用槽等功能，则可将其移动到 blob。  如果将 1.x 应用升级到 2.x，并且机密当前在文件存储中，则将重置机密。
* 为提高性能，会删除“webhook”类型触发器并替换为“HTTP”触发器。
* 对于其中一个属性，主机配置 (`host.json`) 应为空或包含 `2.0` 的 `version`。
* 为改进监视和可观察性，会将 WebJobs 仪表板 (`AzureWebJobsDashboard`) 替换为 [Azure Application Insights](functions-monitoring.md) (`APPINSIGHTS_INSTRUMENTATIONKEY`)
* 应用程序设置 (`local.settings.json`) 需要 `FUNCTIONS_WORKER_RUNTIME` 属性的值，该值映射到应用 `dotnet | node | java | python` 的语言。
    * 为改进占用情况和缩短启动时间，应用仅限于一种语言。 可以发布多个应用，以便为同一解决方案提供使用不同语言的函数。
* 应用服务计划中函数的默认超时时间为 30 分钟。  这仍然可以手动设置为不受限制。
* [由于 .NET core 限制](https://github.com/Azure/azure-functions-host/issues/3414)，已删除 F# 函数的 `.fsx` 脚本。 已编译 F# 函数仍受支持。
* 基于 webhook 的触发器（例如事件网格）的格式已更改为 `https://{app}/runtime/webhooks/{triggerName}`

### <a name="upgrading-a-locally-developed-application"></a>升级本地开发的应用程序

如果是在本地开发的 v1.x 应用，则可以对应用或项目进行更改，使其与 v2 兼容。  建议创建新应用并通过端口将代码复制到新应用。  虽然可以对现有应用进行更改以执行就地升级，但 v1 和 v2 之间还有许多其他改进，而旧版代码可能未利用这些改进（例如，C# 中从 `TraceWriter` 到 `ILogger` 的更改）。  

建议的路径从其中一个 v2 模板开始，并将代码移动到新项目或应用中。

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 运行时版本

在 Visual Studio 中，可在创建项目时选择运行时版本。  Visual Studio 具有这两个主要版本的位，并可以为项目动态利用适合的版本。  这些设置派生自 `.csproj` 文件。  对于 1.x 应用，项目具有以下属性

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

v2 中的项目属性是

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

单击“调试”或“发布”将正确设置项目设置的适当版本。

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 和 Azure Functions Core Tools

其他本地工具依赖于 Azure Functions Core Tools。  这些工具安装在计算机上，通常一次仅在开发计算机上安装一种版本。  请参阅[如何安装特定版本的核心工具的说明](./functions-run-local.md)。

对于 VS Code，可能还需要更新 `azureFunctions.projectRuntime` 的用户设置，以与安装的工具版本匹配。  这还将更新创建新应用期间出现的模板和语言。

### <a name="changing-version-of-apps-in-azure"></a>在 Azure 中更改应用版本

已发布的应用版本通过应用程序设置 `FUNCTIONS_RUNTIME_VERSION` 设置。  对于 v2 应用，将其设置为 `~2`，对于 v1 应用，则为 `~1`。  在不更改函数的代码的情况下，强烈建议不要更改应用（具有已发布到其中的现有函数）的运行时版本。  建议的路径是创建新函数应用并设置为适当版本、测试更改，然后禁用或删除先前的应用。

## <a name="bindings"></a>绑定 

运行时 2.x 使用新的[绑定扩展性模型](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)，该模型可提供以下优势：

* 支持第三方绑定扩展。
* 运行时和绑定分离。 这允许对绑定扩展进行版本控制和单独发布。 例如，可以选择升级到依赖于基础 SDK 的较新版本的扩展版本。
* 更轻便的执行环境，其中运行时仅知道和加载正在使用的绑定。

所有内置 Azure Functions 绑定已采用此模型，默认情况下不再包含，但计时器触发器和 HTTP 触发器除外。 通过 Visual Studio 等工具或通过门户创建函数时，将自动安装这些扩展。

下表指示每个运行时版本支持的绑定。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [在本地对 Azure Functions 进行编码和测试](functions-run-local.md)
* [如何面向 Azure Functions 运行时版本](set-runtime-version.md)
* [发行说明](https://github.com/Azure/azure-functions-host/releases)
