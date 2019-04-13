---
title: 配置 ASP.NET Core 应用程序-Azure 应用服务 |Microsoft Docs
description: 了解如何配置 ASP.NET Core 应用，以便在 Azure 应用服务中
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551107"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>配置 Linux 的 Azure 应用服务的 ASP.NET Core 应用

必须为编译的二进制文件部署 ASP.NET Core 应用。 Visual Studio 发布工具生成解决方案并部署已编译的二进制文件直接，而应用服务部署引擎最初部署代码存储库并进行编译的二进制文件。

本指南提供的重要概念并说明适用于 ASP.NET Core 的开发人员在应用服务中使用内置的 Linux 容器。 如果你从未使用过 Azure 应用服务，请按照[ASP.NET Core 快速入门](quickstart-dotnetcore.md)并[ASP.NET Core 和 SQL 数据库教程](tutorial-dotnetcore-sqldb-app.md)第一个。

## <a name="show-net-core-version"></a>显示.NET Core 版本

若要显示当前的.NET Core 版本，请运行以下命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要显示所有受支持的.NET Core 版本，请运行以下命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>设置.NET Core 版本

在中运行以下命令[Cloud Shell](https://shell.azure.com)将.NET Core 版本为 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，你可以[设置应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)外部应用程序代码。 然后您可以访问它们使用标准 ASP.NET 模式：

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

如果具有相同名称在应用服务中并在配置的应用设置*Web.config*，应用服务值将优先于 Web.config 值。 Web.config 值可用于调试应用本地，但应用服务值可让你运行产品的生产设置中的应用程序。 连接字符串的工作方式相同。 这样一来，可以确保应用程序密码的外部代码存储库并不更改代码的情况下访问相应的值。

## <a name="get-detailed-exceptions-page"></a>获取详细的异常页面

当 ASP.NET 应用程序将引发异常，Visual Studio 调试器中，浏览器将显示详细的异常页，但在应用服务该页面被替换为一个泛型**HTTP 500**错误或**错误时出错处理你的请求。** 。 若要在应用服务中显示详细的异常页，添加`ASPNETCORE_ENVIRONMENT`应用设置为你的应用中运行以下命令<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>检测 HTTPS 会话

在应用服务中，[SSL 终止](https://wikipedia.org/wiki/TLS_termination_proxy)在网络负载均衡器上发生，因此，所有 HTTPS 请求将以未加密的 HTTP 请求形式访问你的应用。 如果你的应用逻辑需要知道如果用户请求或不加密，将配置为转发标头中的中间件*Startup.cs*:

- 配置与中间件[ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions)转发`X-Forwarded-For`并`X-Forwarded-Proto`中的标头`Startup.ConfigureServices`。
- 将专用 IP 地址范围添加到已知的网络，以便中间件可以信任的应用服务负载均衡器中。
- 调用[UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders)中的方法`Startup.Configure`然后再调用其他中间件。

将所有三个元素放在一起，你的代码看起来如下例所示：

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

当 ASP.NET 存储库部署到部署引擎 *.csproj*文件位于根目录，则引擎会将项目部署。 当你部署使用的 ASP.NET 存储库 *.sln*根目录引擎中的文件会提取的第一个网站或 Web 应用程序项目会在有作为应用服务应用。 很可能引擎不选取所需的项目。

若要部署的多项目解决方案，可以指定要在应用服务中使用两种不同方式的项目：

### <a name="using-deployment-file"></a>使用.deployment 文件

添加 *.deployment*文件到存储库根路径，然后添加以下代码：

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>使用应用设置

在中<a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>，通过运行以下 CLI 命令的应用设置添加到应用服务应用。 替换*\<应用名称 >*， *\<资源组名称 >*，以及*\<项目名称 >* 使用适当的值.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 SQL 数据库的 ASP.NET Core 应用](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Linux 版应用服务常见问题解答](app-service-linux-faq.md)