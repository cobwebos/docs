---
title: 配置 ASP.NET 应用
description: 了解如何在 Azure 应用服务中配置 ASP.NET 应用。 本文介绍最常见的配置任务。
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 30fddaec9ca5d0439beadedf7c5ca6b6c7d51d83
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961697"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>为 Azure 应用服务配置 ASP.NET 应用

> [!NOTE]
> 对于 ASP.NET Core，请参阅[为 Azure 应用服务配置 ASP.NET Core 应用](configure-language-dotnetcore.md)

必须将 ASP.NET 应用作为编译的二进制文件部署到 Azure 应用服务。 Visual Studio 发布工具先构建解决方案，然后直接部署已编译的二进制文件，而应用服务部署引擎则先部署代码存储库，然后编译二进制文件。

本指南为 ASP.NET 开发者提供了重要概念和说明。 如果你从未使用过 Azure 应用服务，则应首先按照 [ASP.NET 快速入门](quickstart-dotnet-framework.md)和[将 ASP.NET 与 SQL 数据库配合使用教程](app-service-web-tutorial-dotnet-sqldatabase.md)进行操作。

## <a name="show-supported-net-framework-runtime-versions"></a>显示受支持的 .NET Framework 运行时版本

在应用服务中，Windows 实例已安装了所有受支持的 .NET Framework 版本。 若要显示可供使用的 .NET Framework 运行时和 SDK 版本，请导航到 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 并在基于浏览器的控制台中运行相应的命令：

对于 CLR 4 运行时版本（.NET Framework 4 及更高版本）：

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

最新 .NET Framework 版本可能不会立即可用。

对于 CLR 2 运行时版本（.NET Framework 3.5 及更低版本）：

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>显示当前的 .NET Framework 运行时版本

在 [Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

值为 `v4.0` 表示使用的是最新的 CLR 4 版本 (.NET Framework 4.x)。 值为 `v2.0` 表示使用的是 CLR 2 版本 (.NET Framework 3.5)。

## <a name="set-net-framework-runtime-version"></a>设置 .NET Framework 运行时版本

默认情况下，应用服务使用受支持的最新 .NET Framework 版本来运行 ASP.NET 应用。 若要改为使用 .NET Framework 3.5 运行应用，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令 (V2.0 表示 CLR 2) ：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](configure-common.md#configure-app-settings)和连接字符串。 然后，你可以使用标准 ASP.NET 模式在任何类中访问它们：

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

如果你在应用服务中和 web.config 中配置了具有相同名称的应用设置，则应用服务值将优先于 web.config 值。 本地 web.config 值允许你在本地调试应用，但应用服务值允许你使用生产设置在生产中运行应用。 连接字符串的使用方式与此相同。 这样，你可以将应用程序机密保存在代码存储库外部，无需更改代码便可访问相应的值。

## <a name="deploy-multi-project-solutions"></a>部署多项目解决方案

如果 Visual Studio 解决方案包含多个项目，则说明 Visual Studio 发布过程已包括选择要部署的项目的操作。 当你部署到应用服务部署引擎（例如，使用 Git 或 ZIP 部署）时，如果启用了“生成自动化”，则应用服务部署引擎会选取它发现的第一个网站或 Web 应用程序项目作为应用服务应用。 你可以通过指定 `PROJECT` 应用设置来指定应用服务应当使用哪个项目。 例如，在 [Cloud Shell](https://shell.azure.com)中运行以下内容：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>获取详细的异常页面

当 ASP.NET 应用在 Visual Studio 调试器中生成异常时，浏览器会显示一个详细的异常页面，但在应用服务中，该页面被常规错误消息替换。 若要在应用服务中显示详细的异常页面，请打开 Web.config 文件，在 `<system.web>` 元素下添加 `<customErrors mode="Off"/>` 元素。 例如：

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

使用更新后的 Web.config 重新部署你的应用。你现在应会看到相同的详细异常页面。

## <a name="access-diagnostic-logs"></a>访问诊断日志

你可以使用 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 在应用程序代码中添加诊断消息。 例如： 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 SQL 数据库在 Azure 中生成 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)