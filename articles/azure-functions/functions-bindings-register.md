---
title: 注册 Azure Functions 绑定扩展
description: 了解如何根据你的环境注册 Azure Functions 绑定扩展。
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: a045ef0fea70347f168e8ae0cc93e0c359f31dfa
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031110"
---
# <a name="register-azure-functions-binding-extensions"></a>注册 Azure Functions 绑定扩展

从 Azure Functions 版本2.x 开始，[绑定](./functions-triggers-bindings.md)作为函数运行时中的单独包提供。 虽然 .NET 函数通过 NuGet 包访问绑定，但扩展捆绑包允许其他函数通过配置设置访问所有绑定。

请考虑以下与绑定扩展相关的项：

- 除非[使用 Visual Studio 创建 C# 类库](#local-csharp)，否则绑定扩展不会在 Functions 1.x 中显式注册。

- 默认情况下支持 HTTP 和计时器触发器，不需要扩展。

下表指明了何时以及如何注册绑定。

| 开发环境 |注册<br/> Functions 1.x 中注册  |注册<br/> 在函数 1.x/2.x 中  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 门户|自动|自动<sup>*</sup>|
|非 .NET 语言或本地 Azure Core Tools 开发|自动|[使用 Azure Functions Core Tools 和扩展捆绑包](#extension-bundles)|
|使用 Visual Studio 的 C# 类库|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 类库|不适用|[使用 .NET Core CLI](#vs-code)|

<sup>*</sup>门户使用扩展捆绑。

## <a name="extension-bundles"></a><a name="extension-bundles"></a>扩展捆绑

扩展捆绑是一种向函数应用添加一组兼容的函数绑定扩展的方法。 使用捆绑包时，会在生成应用时添加一组预定义的扩展。 验证捆绑中定义的扩展包是否彼此兼容，这有助于避免包之间发生冲突。 扩展捆绑使你可以避免必须使用 non-.NET 函数项目发布 .NET 项目代码。 可以在应用的 host.json 文件中启用扩展捆绑。  

可以将扩展捆绑与 2.x 版和更高版本的 Functions 运行时一起使用。 

对于使用 Azure Functions Core Tools、Visual Studio Code 进行的本地开发，以及在远程生成时，请使用扩展捆绑。 进行本地开发时，请确保使用最新版本的 [Azure Functions Core Tools](functions-run-local.md#v2)。 开发 Azure 门户中的函数时，也会使用扩展捆绑。 

如果不使用扩展捆绑，则必须先在本地计算机上安装 .NET Core 2.x SDK，才能[显式安装任何绑定扩展](#explicitly-install-extensions)。 将向你的项目添加一个显式定义所需扩展的扩展 .csproj 文件。 对于本地开发，扩展捆绑消除了这些要求。 

若要使用扩展捆绑，请更新 *host json*文件以包含 `extensionBundle` 的以下条目：
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="explicitly-install-extensions"></a>显式安装扩展

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="nuget-packages"></a><a name="local-csharp"></a>NuGet 包

对于基于 c # 类库的函数项目，你应该安装扩展捆绑，专用于不属于类的项目 

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>使用 Visual Studio 的 C\# 类库

在 **Visual Studio** 中，可以使用 [Install-Package](/nuget/tools/ps-ref-install-package) 命令从包管理器控制台安装包，如以下示例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

用于给定绑定的包的名称在该绑定的参考文章中提供。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#functions-1x)。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 有效版本列在[NuGet.org](https://nuget.org)上的各个包页面上。对应于函数运行时1.x 或2.x 的主要版本是在绑定的参考文章中指定的。

如果使用 `Install-Package` 来引用绑定，则无需使用[扩展捆绑](#extension-bundles)。 此方法特定于在 Visual Studio 中生成的类库。

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>带有 Visual Studio Code 的 c # 类库

在 **Visual Studio Code** 中，请使用 .NET Core CLI 中的 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令通过命令提示符安装 C# 类库项目的包。 以下示例演示如何添加绑定：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用于 Azure Functions 2.x 开发。

将 `<BINDING_TYPE_NAME>` 替换为包含所需绑定的包的名称。 可以在[支持的绑定列表](./functions-triggers-bindings.md#supported-bindings)中找到所需的绑定参考文章。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 有效版本列在[NuGet.org](https://nuget.org)上的各个包页面上。对应于函数运行时1.x 或2.x 的主要版本是在绑定的参考文章中指定的。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure Functions 触发器和绑定示例](./functions-bindings-example.md)
