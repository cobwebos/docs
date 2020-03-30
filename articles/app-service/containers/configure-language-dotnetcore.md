---
title: 配置 Linux ASP.NET核心应用程序
description: 了解如何为应用配置预构建ASP.NET核心容器。 本文介绍最常见的配置任务。
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255915"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>为 Azure 应用服务配置 Linux ASP.NET核心应用

ASP.NET核心应用必须部署为已编译的二进制文件。 Visual Studio 发布工具生成解决方案，然后直接部署已编译的二进制文件，而 App Service 部署引擎首先部署代码存储库，然后编译二进制文件。

本指南为在应用服务中使用内置 Linux 容器的ASP.NET核心开发人员提供了关键概念和说明。 如果您从未使用过 Azure 应用服务，请先使用 SQL 数据库教程ASP.NET[核心快速入门](quickstart-dotnetcore.md)[和ASP.NET核心。](tutorial-dotnetcore-sqldb-app.md)

## <a name="show-net-core-version"></a>显示 .NET 核心版本

要显示当前 .NET Core 版本，可在[云壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

要显示所有受支持的 .NET Core 版本，可在[云外壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>设置 .NET 核心版本

在[云壳](https://shell.azure.com)中运行以下命令，将 .NET Core 版本设置为 2.1：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>自定义构建自动化

如果使用打开的 Git 或 zip 包部署应用，应用服务将构建自动化步骤，执行以下顺序：

1. 如果由`PRE_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。
1. 运行`dotnet restore`以还原 NuGet 依赖项。
1. 运行`dotnet publish`以生成用于生产的二进制文件。
1. 如果由`POST_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。

`PRE_BUILD_COMMAND`和`POST_BUILD_COMMAND`是默认情况下为空的环境变量。 要运行预生成命令，请定义`PRE_BUILD_COMMAND`。 要运行生成后命令，请定义`POST_BUILD_COMMAND`。

下面的示例指定一系列命令的两个变量，这些命令用逗号分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关自定义生成自动化的其他环境变量，请参阅[Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

有关应用服务如何在 Linux 中运行和构建ASP.NET核心应用的详细信息，请参阅[Oryx 文档：如何检测和构建 .NET Core 应用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)。

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然后，您可以使用标准ASP.NET核心依赖项注入模式在任何类中访问它们：

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

如果在应用服务和应用*设置.json*中配置具有相同名称的应用设置，则应用服务值优先于*appsettings.json*值。 本地*应用设置.json*值允许您在本地调试应用，但应用服务值允许您在具有生产设置的产品中运行应用。 连接字符串的工作方式相同。 这样，您可以将应用程序机密保存在代码存储库之外，并在不更改代码的情况下访问适当的值。

## <a name="get-detailed-exceptions-page"></a>获取详细的异常页面

当ASP.NET应用在 Visual Studio 调试器中生成异常时，浏览器将显示一个详细的异常页，但在 App Service 中，该页面将被泛型**HTTP 500**错误替换，或者**在处理请求时发生错误。** 。 要在应用服务中显示详细的异常页，通过在`ASPNETCORE_ENVIRONMENT`<a target="_blank" href="https://shell.azure.com" >云外壳</a>中运行以下命令，将应用设置添加到应用。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>检测 HTTPS 会话

在应用服务中，[SSL 终止](https://wikipedia.org/wiki/TLS_termination_proxy)在网络负载均衡器上发生，因此，所有 HTTPS 请求将以未加密的 HTTP 请求形式访问你的应用。 如果应用逻辑需要知道用户请求是否加密，*请Startup.cs*配置转发的标头中间件：

- 使用 [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 配置中间件，以转接 `Startup.ConfigureServices` 中的 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。
- 将专用 IP 地址范围添加到已知网络，以便中间件可以信任应用服务负载均衡器。
- 在调用其他中间件之前，在`Startup.Configure`中调用[使用前眉](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders)方法。

将所有三个元素放在一起，代码如下所示：

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

有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)。

## <a name="deploy-multi-project-solutions"></a>部署多项目解决方案

将ASP.NET存储库部署到根目录中包含 *.csproj*文件的部署引擎时，引擎将部署该项目。 在根目录中部署包含 *.sln*文件的ASP.NET存储库时，引擎将选取它找到的第一个 Web 站点或 Web 应用程序项目作为应用服务应用。 发动机可能不选择您想要的项目。

要部署多项目解决方案，可以通过两种不同的方式指定要在应用服务中使用的项目：

### <a name="using-deployment-file"></a>使用 .部署文件

将 *.部署*文件添加到存储库根，并添加以下代码：

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>使用应用设置

在<a target="_blank" href="https://shell.azure.com">Azure 云外壳中</a>，通过运行以下 CLI 命令向应用服务应用添加应用设置。 将*\<应用名称>、**\<资源组名称>* 和*\<项目名称>* 替换为相应的值。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 SQL 数据库ASP.NET核心应用](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](app-service-linux-faq.md)
