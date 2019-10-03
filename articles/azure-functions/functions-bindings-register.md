---
title: 注册 Azure Functions 绑定扩展
description: 了解如何根据你的环境注册 Azure Functions 绑定扩展。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 93ced443a73d5499d8b305770c3c866c26d540f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086468"
---
# <a name="register-azure-functions-binding-extensions"></a>注册 Azure Functions 绑定扩展

在 Azure Functions 版本 2.x 中，[绑定](./functions-triggers-bindings.md)以独立于函数运行时的包的形式提供。 虽然 .NET 函数通过 NuGet 包访问绑定，但扩展捆绑包允许其他函数通过配置设置访问所有绑定。

请考虑以下与绑定扩展相关的项：

- 除非[使用 Visual Studio 创建 C# 类库](#local-csharp)，否则绑定扩展不会在 Functions 1.x 中显式注册。

- 默认情况下支持 HTTP 和计时器触发器，不需要扩展。

下表指明了何时以及如何注册绑定。

| 开发环境 |注册<br/> Functions 1.x 中注册  |注册<br/> Functions 2.x 中注册  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 门户|自动|自动|
|非 .NET 语言或本地 Azure Core Tools 开发|自动|[使用 Azure Functions Core Tools 和扩展捆绑包](#extension-bundles)|
|使用 Visual Studio 的 C# 类库|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 类库|不可用|[使用 .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>用于本地开发的扩展捆绑

扩展捆绑是适用于版本 2.x 运行时的一种本地开发技术，可让你将一组兼容的 Functions 绑定扩展添加到函数应用项目。 部署到 Azure 时，这些扩展包将包含在部署包中。 捆绑使得 Microsoft 发布的所有绑定都可以通过 *host.json* 文件中的设置使用。 捆绑中定义的扩展包彼此兼容，这有助于避免包之间发生冲突。 进行本地开发时，请确保使用最新版本的 [Azure Functions Core Tools](functions-run-local.md#v2)。

对于使用 Azure Functions Core Tools 或 Visual Studio Code 进行的所有本地开发，请使用扩展捆绑。

如果不使用扩展捆绑，则必须先在本地计算机上安装 .NET Core 2.x SDK，然后才能安装任何绑定扩展。 捆绑为本地开发消除了这一要求。 

若要使用扩展捆绑，请更新 *host json*文件以包含 `extensionBundle` 的以下条目：

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

以下属性可在 `extensionBundle` 中使用：

| 属性 | 描述 |
| -------- | ----------- |
| **`id`** | Microsoft Azure 函数扩展包的命名空间。 |
| **`version`** | 要安装的捆绑的版本。 Functions 运行时始终选取按版本范围或间隔定义的最宽松版本。 上述版本值允许使用从 1.0.0 到 2.0.0（不含 2.0.0）的所有捆绑版本。 有关详细信息，请参阅[用于指定版本范围的间隔表示法](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)。 |

捆绑中的包发生变化时，捆绑版本将会递增。 当捆绑中的包递增一个主要版本号时，主要版本将发生变化，这通常与 Functions 运行时的主要版本更改行为相一致。  

默认捆绑安装的当前扩展集将在此 [extensions.json 文件](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)中枚举。

<a name="local-csharp"></a>

## <a name="vs"></a>使用 Visual Studio 的 C\# 类库

在 **Visual Studio** 中，可以使用 [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 命令从包管理器控制台安装包，如以下示例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

用于给定绑定的包的名称在该绑定的参考文章中提供。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#packages---functions-1x)。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

如果使用 `Install-Package` 来引用绑定，则无需使用[扩展捆绑](#extension-bundles)。 此方法特定于在 Visual Studio 中生成的类库。

## <a name="vs-code"></a> 使用 Visual Studio Code 的 C# 类库

> [!NOTE]
> 我们建议使用[扩展捆绑](#extension-bundles)来让 Functions 自动安装一组兼容的绑定扩展包。

在 **Visual Studio Code** 中，请使用 .NET Core CLI 中的 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 命令通过命令提示符安装 C# 类库项目的包。 以下示例演示如何添加绑定：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用于 Azure Functions 2.x 开发。

请将 `<BINDING_TYPE_NAME>` 替换为参考文章中为所需绑定提供的包名称。 可以在[支持的绑定列表](./functions-triggers-bindings.md#supported-bindings)中找到所需的绑定参考文章。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure Functions 触发器和绑定示例](./functions-bindings-example.md)
