---
title: 配置 Linux ASP.NET Core 应用
description: 了解如何为应用配置预建 ASP.NET Core 容器。 本文介绍最常见的配置任务。
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: cab99b9d20ce8a3190eb9aa59650dab32fca324d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768412"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>为 Azure App Service 配置 Linux ASP.NET Core 应用

必须将 ASP.NET Core 应用部署为已编译的二进制文件。 Visual Studio 发布工具生成解决方案，然后直接部署已编译的二进制文件，而应用服务部署引擎首先部署代码存储库，然后编译二进制文件。

本指南为在应用服务中使用内置 Linux 容器的 ASP.NET Core 开发人员提供重要的概念和说明。 如果你从未使用过 Azure App Service，请先参阅[ASP.NET Core 快速入门](quickstart-dotnetcore.md)和[SQL 数据库的 ASP.NET Core 教程](tutorial-dotnetcore-sqldb-app.md)。

## <a name="show-net-core-version"></a>显示 .NET Core 版本

若要显示当前的 .NET Core 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要显示所有支持的 .NET Core 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>设置 .NET Core 版本

在[Cloud Shell](https://shell.azure.com)中运行以下命令，将 .net Core 版本设置为2.1：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然后，你可以使用标准 ASP.NET Core 依赖关系注入模式在任何类中访问它们：

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

例如，如果在应用服务和*appsettings*中配置具有相同名称的应用设置，则应用服务值优先于*appsettings*值。 利用本地*appsettings*值，你可以在本地调试应用程序，但应用服务值允许你在产品和生产设置中运行应用。 连接字符串的工作方式与此相同。 这样，你可以将应用程序机密保存在代码存储库外，并访问适当的值，而无需更改代码。

## <a name="get-detailed-exceptions-page"></a>获取详细的异常页

当 ASP.NET 应用在 Visual Studio 调试器中生成异常时，浏览器将显示一个详细的异常页，但在应用服务中，该页面将被通用**HTTP 500**错误替换或在**处理请求时出错。** 。 若要在应用服务中显示详细的异常页面，请在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中运行以下命令，将 `ASPNETCORE_ENVIRONMENT` 应用设置添加到应用。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>检测 HTTPS 会话

在应用服务中，[SSL 终止](https://wikipedia.org/wiki/TLS_termination_proxy)在网络负载均衡器上发生，因此，所有 HTTPS 请求将以未加密的 HTTP 请求形式访问你的应用。 如果应用逻辑需要知道用户请求是否已加密，请在*Startup.cs*中配置转发的标头中间件：

- 使用 [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 配置中间件，以转接 `Startup.ConfigureServices` 中的 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。
- 向已知网络添加专用 IP 地址范围，以便中间件可以信任应用服务负载均衡器。
- 调用其他中间件之前, 调用`Startup.Configure`中的 [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) 方法。

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

有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)。

## <a name="deploy-multi-project-solutions"></a>部署多项目解决方案

当你使用根目录中的 *.csproj*文件将 ASP.NET 存储库部署到部署引擎时，该引擎将部署项目。 当你在根目录中部署带有 *.sln*文件的 ASP.NET 存储库时，引擎将选取它作为应用服务应用找到的第一个网站或 Web 应用程序项目。 引擎可能不会选择所需的项目。

若要部署多项目解决方案，可以通过两种不同的方式指定要在应用服务中使用的项目：

### <a name="using-deployment-file"></a>使用 deployment 文件

将一个 *. 部署*文件添加到存储库根，并添加以下代码：

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>使用应用程序设置

在<a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>中，通过运行以下 CLI 命令将应用设置添加到应用服务应用。 用适当的值替换 *\<应用名称 >* 、 *\<资源组名称 >* 和 *\<项目名称 >* 。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程： ASP.NET Core 包含 SQL 数据库的应用](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](app-service-linux-faq.md)
