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
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740017"
---
# <a name="register-azure-functions-binding-extensions"></a>注册 Azure Functions 绑定扩展

Azure Functions 支持 HTTP 和现成的计时器。 若要使用其他服务，你需要安装或*注册*[绑定](./functions-triggers-bindings.md)扩展。 绑定扩展是通过 Azure Core Tools 或 NuGet 程序包提供的。 

下表指明了何时以及如何注册绑定。

| 开发环境 |注册<br/> Functions 1.x 中注册  |注册<br/> Functions 2.x 中注册  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 门户|自动|[自动，带有提示](#azure-portal-development)|
|非 .NET 语言或本地 Azure Core Tools 开发|自动|[使用 Core Tools CLI 命令](#local-development-azure-functions-core-tools)|
|使用 Visual Studio 2017 的 C# 类库|[使用 NuGet 工具](#c-class-library-with-visual-studio-2017)|[使用 NuGet 工具](#c-class-library-with-visual-studio-2017)|
|使用 Visual Studio Code 的 C# 类库|不适用|[使用 .NET Core CLI](#c-class-library-with-visual-studio-code)|

以下绑定类型例外，它们不需要显式注册，因为它们会在所有版本和环境中自动注册：HTTP 和计时器。

> [!IMPORTANT]
> 本文的其余内容仅适用于 Functions 2.x。 在 Functions 1.x 中，除了[使用 Visual Studio 2017 创建 C# 类库](#local-csharp)时，绑定扩展不是显式注册的。

## <a name="azure-portal-development"></a>使用 Azure 门户进行开发

创建函数或添加绑定时，如果触发器或绑定的扩展需要注册，则系统会显示提示。 单击“安装”注册扩展，以响应提示。 在消耗计划中，安装最多需要 10 分钟。

对于给定的函数应用，只需安装每个扩展一次。 若要获取门户中不可用的受支持绑定，或要更新已安装扩展，也可以[在门户中手动安装或更新 Azure Functions 绑定扩展](install-update-binding-extensions-manual.md)。  

## <a name="local-development-azure-functions-core-tools"></a>使用 Azure Functions Core Tools 进行本地开发

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>使用 Visual Studio 2017 的 C# 类库

在“Visual Studio 2017”中，可使用 [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 命令从包管理器控制台安装包，如以下示例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

用于给定绑定的包的名称在该绑定的参考文章中提供。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#packages---functions-1x)。

将示例中的 `<target_version>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

## <a name="c-class-library-with-visual-studio-code"></a>使用 Visual Studio Code 的 C# 类库

在“Visual Studio Code”中，可在 .NET Core CLI 中，通过命令提示符使用 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 命令来安装包，如以下示例所示：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

.NET Core CLI 只能用于 Azure Functions 2.x 开发。

用于给定绑定的包的名称在该绑定的参考文章中提供。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#packages---functions-1x)。

将示例中的 `<target_version>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure Functions 触发器和绑定示例](./functions-bindings-example.md)

