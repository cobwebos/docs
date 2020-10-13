---
title: 配置 ASP.NET Core 应用
description: 了解如何在原生 Windows 实例、预构建的 Linux 容器或 Azure 应用服务中配置 ASP.NET Core 应用。 本文介绍最常见的配置任务。
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 3456adc2b143f1f51115183fe4873938d067d267
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961663"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>为 Azure 应用服务配置 ASP.NET Core 应用

> [!NOTE]
> 对于 .NET Framework 中的 ASP.NET，请参阅[为 Azure 应用服务配置 ASP.NET 应用](configure-language-dotnet-framework.md)

必须将 ASP.NET Core 应用作为编译的二进制文件部署到 Azure 应用服务。 Visual Studio 发布工具先构建解决方案，然后直接部署已编译的二进制文件，而应用服务部署引擎则先部署代码存储库，然后编译二进制文件。

本指南为 ASP.NET Core 开发者提供了重要概念和说明。 如果你从未使用过 Azure 应用服务，则应首先按照 [ASP.NET Core 快速入门](quickstart-dotnetcore.md)和[将 ASP.NET Core 与 SQL 数据库配合使用教程](tutorial-dotnetcore-sqldb-app.md)进行操作。

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>显示受支持的 .NET Core 运行时版本

在应用服务中，Windows 实例已安装了所有受支持的 .NET Core 版本。 若要显示可供使用的 .NET Core 运行时和 SDK 版本，请导航到 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 并在基于浏览器的控制台中运行以下命令：

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>显示 .NET Core 版本

若要显示当前的 .NET Core 版本，请在 [Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要显示所有支持的 .NET Core 版本，请在 [Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>设置 .NET Core 版本

::: zone pivot="platform-windows"  

在项目文件中为 ASP.NET Core 项目设置目标框架。 有关详细信息，请参阅 .NET Core 文档中的[选择要使用 的 .NET Core 版本](/dotnet/core/versions/selection)。

::: zone-end

::: zone pivot="platform-linux"

在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，将 .net Core 版本设置为3.1：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>自定义生成自动化

如果在启用生成自动化的情况下使用 Git 或 zip 包部署应用，应用服务生成自动化将按以下顺序完成各个步骤：

1. 运行 `PRE_BUILD_SCRIPT_PATH` 指定的自定义脚本。
1. 运行 `dotnet restore` 来还原 NuGet 依赖项。
1. 运行 `dotnet publish` 来构建用于生产的二进制文件。
1. 运行 `POST_BUILD_SCRIPT_PATH` 指定的自定义脚本。

`PRE_BUILD_COMMAND` 和 `POST_BUILD_COMMAND` 是默认为空的环境变量。 若要运行生成前命令，请定义 `PRE_BUILD_COMMAND`。 若要运行生成后命令，请定义 `POST_BUILD_COMMAND`。

以下示例在一系列命令中指定两个以逗号分隔的变量。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关用于自定义生成自动化的其他环境变量，请参阅 [Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

若要详细了解应用服务如何在 Linux 中运行和构建 ASP.NET Core 应用，请参阅 [Oryx 文档：如何检测和构建 .NET Core 应用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)。

::: zone-end

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](configure-common.md#configure-app-settings)。 然后，你可以使用标准 ASP.NET Core 依赖项注入模式在任何类中访问它们：

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

例如，如果你在应用服务中和 appsettings.json 中配置了具有相同名称的应用设置，则应用服务值将优先于 appsettings.json 值。 本地 appsettings.json 值允许你在本地调试应用，但应用服务值允许你使用生产设置在生产中运行应用。 连接字符串的使用方式与此相同。 这样，你可以将应用程序机密保存在代码存储库外部，无需更改代码便可访问相应的值。

> [!NOTE]
> 请注意，appsettings.json 中的[层次化配置数据](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data)是使用 .NET Core 的标准分隔符 `:` 进行访问的。 若要替代应用服务中的特定层次化配置设置，请在密钥中设置具有相同分隔格式的应用设置名称。 可以在 [Cloud Shell](https://shell.azure.com)中运行以下示例：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>部署多项目解决方案

如果 Visual Studio 解决方案包含多个项目，则说明 Visual Studio 发布过程已包括选择要部署的项目的操作。 当你部署到应用服务部署引擎（例如，使用 Git 或 ZIP 部署）时，如果启用了“生成自动化”，则应用服务部署引擎会选取它发现的第一个网站或 Web 应用程序项目作为应用服务应用。 你可以通过指定 `PROJECT` 应用设置来指定应用服务应当使用哪个项目。 例如，在 [Cloud Shell](https://shell.azure.com)中运行以下内容：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>访问诊断日志

ASP.NET Core 提供了[适用于应用服务的内置日志记录提供程序](/aspnet/core/fundamentals/logging/#azure-app-service)。 在项目的 *Program.cs* 中，通过 `ConfigureLogging` 扩展方法将该提供程序添加到应用程序，如以下示例所示：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

然后，你可以使用[标准 .NET Core 模式](/aspnet/core/fundamentals/logging)配置并生成日志。

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

若要详细了解如何对应用服务中的 ASP.NET Core 应用进行故障排除，请参阅[对 Azure 应用服务和 IIS 上的 ASP.NET Core 进行故障排除](/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>获取详细的异常页面

当 ASP.NET Core 应用在 Visual Studio 调试器中生成异常时，浏览器会显示一个详细的异常页面，但在应用服务中，该页面被替换为一般 **HTTP 500** 错误或**处理请求时发生的某个错误**。 。 若要在应用服务中显示详细的异常页面，请 `ASPNETCORE_ENVIRONMENT` 在 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中运行以下命令，将应用设置添加到应用。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>检测 HTTPS 会话

在应用服务中，[SSL 终止](https://wikipedia.org/wiki/TLS_termination_proxy)在网络负载均衡器上发生，因此，所有 HTTPS 请求将以未加密的 HTTP 请求形式访问你的应用。 如果你的应用逻辑需要知道用户请求是否已加密，请在 *Startup.cs* 中配置 Forwarded Headers 中间件：

- 使用 [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 配置中间件，以转接 `Startup.ConfigureServices` 中的 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。
- 添加已知网络的专用 IP 地址范围，以便该中间件可以信任应用服务负载均衡器。
- 在调用其他中间件之前在 `Startup.Configure` 中调用 [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) 方法。

将所有这三个元素放在一起，代码类似于以下示例：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](/aspnet/core/host-and-deploy/proxy-load-balancer)。

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：将 ASP.NET Core 应用与 SQL 数据库配合使用](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](faq-app-service-linux.md)

::: zone-end