---
title: 注册 Azure Functions 绑定扩展
description: 了解如何根据你的环境注册 Azure Functions 绑定扩展。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 802e177b6f3844abe4d24c26b7ea2d0d4fb1688c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697010"
---
# <a name="register-azure-functions-binding-extensions"></a>注册 Azure Functions 绑定扩展

从 Azure Functions 版本 2.x 中，[绑定](./functions-triggers-bindings.md)都可用作函数运行时中的单独包。 虽然.NET 函数访问绑定通过 NuGet 包，扩展捆绑包将允许通过配置设置的所有绑定到其他函数的访问。

请考虑与绑定扩展相关的以下项：

- 在 Functions 1.x 中，除了[使用 Visual Studio 2017 创建 C# 类库](#local-csharp)时，绑定扩展不是显式注册的。

- HTTP 和计时器触发器支持默认情况下，不需要扩展。

下表指明了何时以及如何注册绑定。

| 开发环境 |注册<br/> Functions 1.x 中注册  |注册<br/> Functions 2.x 中注册  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 门户|自动|自动|
|非 .NET 语言或本地 Azure Core Tools 开发|自动|[使用 Azure Functions 核心工具和扩展捆绑包](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|使用 Visual Studio 2017 的 C# 类库|[使用 NuGet 工具](#c-class-library-with-visual-studio-2017)|[使用 NuGet 工具](#c-class-library-with-visual-studio-2017)|
|使用 Visual Studio Code 的 C# 类库|不适用|[使用 .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>使用 Azure Functions 核心工具和扩展捆绑包的本地开发

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>使用 Visual Studio 2017 的 C# 类库

在“Visual Studio 2017”中，可使用 [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 命令从包管理器控制台安装包，如以下示例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

该绑定的情况下，参考文章中提供的针对给定的绑定使用的包名称。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#packages---functions-1x)。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

## <a name="c-class-library-with-visual-studio-code"></a>使用 Visual Studio Code 的 C# 类库

在“Visual Studio Code”中，可在 .NET Core CLI 中，通过命令提示符使用 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 命令来安装包，如以下示例所示：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

.NET Core CLI 只能用于 Azure Functions 2.x 开发。

用于给定绑定的包的名称在该绑定的参考文章中提供。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#packages---functions-1x)。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure Functions 触发器和绑定示例](./functions-bindings-example.md)

