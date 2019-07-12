---
title: 注册 Azure Functions 绑定扩展
description: 了解如何根据你的环境注册 Azure Functions 绑定扩展。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 5969c3e0d270b45347f8132b2d655ba2e56cb2c0
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625898"
---
# <a name="register-azure-functions-binding-extensions"></a>注册 Azure Functions 绑定扩展

在 Azure Functions 版本 2.x 中，[绑定](./functions-triggers-bindings.md)以独立于函数运行时的包的形式提供。 虽然 .NET 函数通过 NuGet 包访问绑定，但扩展捆绑包允许其他函数通过配置设置访问所有绑定。

请考虑以下与绑定扩展相关的项：

- 在函数中不显式注册绑定扩展 1.x 时除外[创建C#使用 Visual Studio 类库](#local-csharp)。

- 默认情况下支持 HTTP 和计时器触发器，不需要扩展。

下表指明了何时以及如何注册绑定。

| 开发环境 |注册<br/> Functions 1.x 中注册  |注册<br/> Functions 2.x 中注册  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 门户|自动|自动|
|非 .NET 语言或本地 Azure Core Tools 开发|自动|[使用 Azure Functions Core Tools 和扩展捆绑包](#extension-bundles)|
|C#使用 Visual Studio 的类库|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 类库|不可用|[使用 .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>扩展捆绑包以进行本地开发

扩展捆绑包是可以添加一兼容的函数绑定到函数应用项目的扩展的版本 2.x 运行时的本地开发技术。 然后部署包中包含这些扩展包，部署到 Azure 时。 捆绑包可以由 Microsoft 通过在设置发布的所有绑定*host.json*文件。 在绑定中定义的扩展包都兼容，从而帮助您避免包之间的冲突。 当开发本地，请确保使用最新版本[Azure Functions Core Tools](functions-run-local.md#v2)。

用于所有本地开发使用 Azure Functions Core Tools 或 Visual Studio Code 扩展捆绑包。

如果不使用扩展捆绑包，则必须安装.NET Core 2.x SDK 之前安装的任何绑定扩展在本地计算机上。 捆绑包中删除以进行本地开发此要求。 

若要使用扩展捆绑包，更新*host.json*文件以包括以下条目`extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

以下属性可用于在`extensionBundle`:

| 属性 | 描述 |
| -------- | ----------- |
| **`id`** | Microsoft Azure Functions 扩展绑定命名空间。 |
| **`version`** | 若要安装的包的版本。 Functions 运行时总是选择接口定义的版本范围或间隔的最高允许版本。 上面指定的版本值允许所有捆绑包版本为 1.0.0，但不是包括 2.0.0。 有关详细信息，请参阅[间隔表示法，用于指定版本范围](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)。 |

作为捆绑包更改中的包捆绑在版本增量。 包捆绑中的增加的主要版本，通常与 Functions 运行时的主版本中的更改时，会发生的主版本更改。  

在此枚举的默认捆绑包安装的扩展当前集[extensions.json 文件](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)。

<a name="local-csharp"></a>

## <a name="vs"></a> C\#使用 Visual Studio 的类库

在中**Visual Studio**，可以从使用程序包管理器控制台安装程序包[Install-package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package)命令，如下面的示例中所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

用于给定绑定的包的名称在该绑定的参考文章中提供。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#packages---functions-1x)。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

如果您使用`Install-Package`若要引用一个绑定，您不必使用[扩展捆绑包](#extension-bundles)。 这种方法是特定于 Visual Studio 中构建的类库。

## <a name="vs-code"></a> C#使用 Visual Studio Code 的类库

> [!NOTE]
> 我们建议使用[扩展捆绑包](#extension-bundles)能够自动安装兼容的一组绑定扩展包的函数。

在中**Visual Studio Code**，安装的包C#从命令提示符处使用类库项目[dotnet 添加包](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package).NET Core CLI 命令。 下面的示例演示如何添加绑定：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用于 Azure Functions 2.x 开发。

替换为`<BINDING_TYPE_NAME>`与为你所需的绑定参考文章中提供的包的名称。 您可以找到所需的绑定参考文章中[支持的绑定的列表](./functions-triggers-bindings.md#supported-bindings)。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure Functions 触发器和绑定示例](./functions-bindings-example.md)
