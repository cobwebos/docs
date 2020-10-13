---
title: 注册 Azure Functions 绑定扩展
description: 了解如何根据你的环境注册 Azure Functions 绑定扩展。
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88689539"
---
# <a name="register-azure-functions-binding-extensions"></a>注册 Azure Functions 绑定扩展

从 Azure Functions 版本 2.x 开始，函数运行时默认情况下仅包括 HTTP 和计时器触发器。 其他[触发器和绑定](./functions-triggers-bindings.md)可作为单独的包提供。

.NET 类库函数应用使用以 NuGet 包的形式安装在项目中的绑定。 扩展捆绑允许非 .NET 函数应用使用相同的绑定，而不必处理 .NET 基础结构。

下表指明了何时以及如何注册绑定。

| 开发环境 |在<br/> Functions 1.x 中注册  |在<br/> Functions 3.x/2.x 中注册  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 门户|自动|自动<sup>*</sup>|
|非 .NET 语言|自动|使用[扩展捆绑](#extension-bundles)（推荐）或[显式安装扩展](#explicitly-install-extensions)|
|使用 Visual Studio 的 C# 类库|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 类库|不适用|[使用 .NET Core CLI](#vs-code)|

<sup>*</sup> 门户使用扩展捆绑包。

## <a name="access-extensions-in-non-net-languages"></a>非 .NET 语言中的访问扩展

对于 Java、JavaScript、PowerShell、Python 和自定义处理程序函数应用，我们建议使用扩展捆绑来访问绑定。 如果无法使用扩展捆绑，则可以显式安装绑定扩展。

### <a name="extension-bundles"></a><a name="extension-bundles"></a>扩展捆绑包

使用扩展捆绑可将一组兼容的绑定扩展添加到函数应用。 可以在应用的 host.json 文件中启用扩展捆绑。

可以将扩展捆绑与 2.x 版和更高版本的 Functions 运行时一起使用。

扩展捆绑已进行版本控制。 每个版本都包含一组特定的绑定扩展，这些扩展经过验证，可协同工作。 根据应用中所需的扩展，选择捆绑版本。

若要将扩展捆绑添加到函数应用，请将 `extensionBundle` 部分添加到 host.json。 在许多情况下，Visual Studio Code 和 Azure Functions Core Tools 会为你自动添加它。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

下表列出了默认 Microsoft.Azure.Functions.ExtensionBundle 捆绑的当前可用版本以及指向它们所包括的扩展的链接。

| 捆绑版本 | host.json 中的版本 | 已包含的扩展 |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | 请参阅用于生成捆绑的 [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) |
| 2.x | `[2.*, 3.0.0)` | 请参阅用于生成捆绑的 [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) |

> [!NOTE]
> 虽然可以在 host.json 中指定自定义版本范围，但建议使用此表中的版本值。

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>显式安装扩展

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>安装来自 NuGet 的 .NET 语言的扩展

对于基于 C# 类库的函数项目，应直接安装扩展。 扩展捆绑包专用于不基于 C# 类库的项目。

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>使用 Visual Studio 的 C\# 类库

在 **Visual Studio** 中，可以使用 [Install-Package](/nuget/tools/ps-ref-install-package) 命令从包管理器控制台安装包，如以下示例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

用于给定绑定的包的名称在该绑定的参考文章中提供。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#functions-1x)。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

如果使用 `Install-Package` 来引用绑定，则无需使用[扩展捆绑](#extension-bundles)。 此方法特定于在 Visual Studio 中生成的类库。

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> 使用 Visual Studio Code 的 C# 类库

在 **Visual Studio Code** 中，请使用 .NET Core CLI 中的 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令通过命令提示符安装 C# 类库项目的包。 以下示例演示如何添加绑定：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用于 Azure Functions 2.x 开发。

将 `<BINDING_TYPE_NAME>` 替换为包含所需绑定的包的名称。 可以在[支持的绑定列表](./functions-triggers-bindings.md#supported-bindings)中找到所需的绑定参考文章。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure Functions 触发器和绑定示例](./functions-bindings-example.md)
