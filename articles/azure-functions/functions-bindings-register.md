---
title: 注册 Azure Functions 绑定扩展
description: 了解如何根据你的环境注册 Azure Functions 绑定扩展。
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 599becae0225bea623c383ead49cd9abcea6fff2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231110"
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
|使用 Visual Studio Code 的 C# 类库|不适用|[使用 .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>用于本地开发的扩展捆绑

扩展捆绑是一种部署技术，可让你将一组兼容的函数绑定到 function app。 生成应用时，会添加一组预定义的扩展。 捆绑中定义的扩展包彼此兼容，这有助于避免包之间发生冲突。 在应用的 host json 文件中启用扩展捆绑。  

可以将扩展捆绑与版本2.x 和更高版本的函数运行时一起使用。 进行本地开发时，请确保使用最新版本的 [Azure Functions Core Tools](functions-run-local.md#v2)。

使用 Azure Functions Core Tools、Visual Studio Code 以及远程构建时使用扩展捆绑进行本地开发。

如果不使用扩展捆绑，则必须先在本地计算机上安装 .NET Core 2.x SDK，然后才能安装任何绑定扩展。 对于本地开发，扩展捆绑消除了这一要求。 

若要使用扩展捆绑，请更新 *host json*文件以包含 `extensionBundle` 的以下条目：
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a>使用 Visual Studio 的 C\# 类库

在 **Visual Studio** 中，可以使用 [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 命令从包管理器控制台安装包，如以下示例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

用于给定绑定的包的名称在该绑定的参考文章中提供。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#packages---functions-1x)。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 有效版本列在[NuGet.org](https://nuget.org)上的各个包页面上。对应于函数运行时1.x 或2.x 的主要版本是在绑定的参考文章中指定的。

如果使用 `Install-Package` 引用绑定，则无需使用[扩展捆绑](#extension-bundles)。 此方法特定于在 Visual Studio 中生成的类库。

## <a name="vs-code"></a> 使用 Visual Studio Code 的 C# 类库

> [!NOTE]
> 我们建议使用[扩展捆绑](#extension-bundles)来让 Functions 自动安装一组兼容的绑定扩展包。 

在 **Visual Studio Code** 中，请使用 .NET Core CLI 中的 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 命令通过命令提示符安装 C# 类库项目的包。 以下示例演示如何添加绑定：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用于 Azure Functions 2.x 开发。

将 `<BINDING_TYPE_NAME>` 替换为包含所需绑定的包的名称。 可以在[支持的绑定列表](./functions-triggers-bindings.md#supported-bindings)中找到所需的绑定参考文章。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 有效版本列在[NuGet.org](https://nuget.org)上的各个包页面上。对应于函数运行时1.x 或2.x 的主要版本是在绑定的参考文章中指定的。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure Functions 触发器和绑定示例](./functions-bindings-example.md)
