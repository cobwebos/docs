---
title: 适用于 ASP.NET Core 的 Azure Application Insights | Microsoft Docs
description: 监视 Web 应用程序的可用性、性能和使用情况。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: f9ab9b9af81bf1827c2da646908e204bd051706b
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970928"
---
# <a name="application-insights-for-aspnet-core"></a>用于 ASP.NET Core 的 Application Insights

Azure Application Insights 提供 Web 应用程序的监视信息，深度可达代码级别。 使用它可以轻松监视 Web 应用程序的可用性、性能和使用情况。 还可以快速确定并诊断应用程序中的错误，而无需等待用户报告这些错误。

本文逐步讲解如何在 Visual Studio 中创建一个 ASP.NET Core [Razor 页面](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio)示例应用程序，以及如何开始使用 Azure Application Insights 进行监视。

## <a name="prerequisites"></a>先决条件

- NET Core 2.0.0 SDK 或更高版本。
- 装有 ASP.NET 和 Web 开发工作负荷的 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7.3 或更高版本。 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>在 Visual Studio 中创建 ASP.NET Core 项目

1. 单击右键，并管理员身份启动 **Visual Studio 2017**。
2. 选择“文件” > “新建” > “项目”(Ctrl-Shift-N)。

   ![Visual Studio 中“文件”>“新建”>“项目”菜单的屏幕截图](./media/app-insights-asp-net-core/001-new-project.png)

3. 展开“Visual C#”，选择“.NET Core” > “ASP.NET Core Web 应用程序”。 输入“名称” > “解决方案名称”，选中“创建新的 Git 存储库”。

   ![Visual Studio 中“文件”>“新建”>“项目”向导的屏幕截图](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. 选择“.Net Core” > “ASP.NET Core 2.0 Web 应用程序” > “确定”。

    ![Visual Studio 中“文件”>“新建”>“项目”选择菜单的屏幕截图](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights 搜索

默认情况下，在 Visual Studio 版本 2015 Update 2 或更高版本中，如果其中包含基于 ASP.NET Core 2+ 的项目，则可充分利用 [Application Insights 搜索](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)。即使在向项目显式添加 Application Insights 之前，也可以这样做。

若要测试此功能，请执行以下操作：

1. 单击 IIS Express 运行应用 ![Visual Studio 中 IIS Express 图标的屏幕截图](./media/app-insights-asp-net-core/004-iis-express.png)

2. 选择“视图” > “其他窗口” > “Application Insights 搜索”。

   ![Visual Studio 诊断工具的屏幕截图](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. 调试会话遥测目前仅适用于本地分析。 若要完全启用 Application Insights，请选择右上角的“遥测就绪性”，或者按下一部分的步骤操作。

   ![Visual Studio Application Insights 搜索的屏幕截图](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> 若要详细了解 Visual Studio 如何在你向 ASP.NET Core 项目添加 Application Insights 之前在本地启用 [Application Insights 搜索](app-insights-visual-studio.md)和 [CodeLens](app-insights-visual-studio-codelens.md) 之类的功能，请查看[本文末尾](#Application-Insights-search-continued)的说明。

## <a name="add-application-insights-telemetry"></a>添加 Application Insights 遥测

1. 选择“项目” > “添加 Application Insights 遥测...”。（也可右键单击“连接的服务”，然后选择“添加连接的服务”。）

    ![Visual Studio 中“文件”>“新建”>“项目”选择菜单的屏幕截图](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. 选择“入门”。 （根据 Visual Studio 版本的不同，文本可能会稍有不同。 某些较旧的版本仍使用“免费开始”按钮。）

    ![Visual Studio 中“文件”>“新建”>“项目”选择菜单的屏幕截图](./media/app-insights-asp-net-core/008-get-started.png)

3. 选择相应的“订阅” > “资源” > “注册”。

## <a name="changes-made-to-your-project"></a>对项目所做的更改

Application Insights 的系统开销低。 通过添加 Application Insights 遥测来查看对项目所做的修改：

选择“视图” > “团队资源管理器”(Ctrl+\, Ctrl+M) >“项目” > “更改”

- 共有四项更改：

  ![通过添加 Application Insights 更改文件的屏幕截图](./media/app-insights-asp-net-core/009-changes.png)

- 创建了一个新文件：

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- 修改了三个文件：（添加额外注释是为了突出显示所做的更改）

  _appsettings.json_

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
// Changes to file post adding Application Insights Telemetry:
  "ApplicationInsights": {
    "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
  }
}
//
```

  _ContosoDotNetCore.csproj_

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
    <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
<!---->
  </PropertyGroup>
  <ItemGroup>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
<!---->
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
  </ItemGroup>
<!--Changes to file post adding Application Insights Telemetry:-->
  <ItemGroup>
    <WCFMetadata Include="Connected Services" />
  </ItemGroup>
<!---->
</Project>
```

   _Program.cs_

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
// Change to file post adding Application Insights Telemetry:
                .UseApplicationInsights()
//
                .UseStartup<Startup>()
                .Build();
    }
}
```

## <a name="synthetic-transactions-with-powershell"></a>使用 PowerShell 创建综合事务

若要通过综合事务来自动向应用发起请求，请执行以下操作：

1. 单击 IIS Express 运行应用 ![Visual Studio 中 IIS Express 图标的屏幕截图](./media/app-insights-asp-net-core/004-iis-express.png)

2. 复制浏览器地址栏中的 URL。 它采用以下格式：http://localhost:{random 端口号}

   ![浏览器 URL 地址栏的屏幕截图](./media/app-insights-asp-net-core/0013-copy-url.png)

3. 运行以下 PowerShell 循环，针对测试应用创建 100 个综合事务。 修改 **localhost:** 后面的端口号，使之与上一步骤中复制的 URL 相匹配。

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>打开 Application Insights 门户

运行上一部分所述的 PowerShell 后，启动 Application Insights 查看事务并确认正在收集数据。 

在 Visual Studio 菜单中，选择“项目” > “Application Insights” > “打开 Application Insights 门户”

   ![Application Insights 概述的屏幕截图](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> 在上面的示例屏幕截图中，目前尚未收集“实时流”、“页面视图加载时间”和“失败的请求数”。 下一部分将逐步讲解如何添加每项设置。 如果已开始收集“实时流”和“页面视图加载时间”，则只需遵循“失败的请求数”的步骤。

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>收集“失败的请求数”、“实时流”和“页面视图加载时间”

### <a name="failed-requests"></a>失败的请求数

从技术上讲，系统已开始收集“失败的请求数”，但目前并未发生失败的请求。 为了加速该过程，可将一个自定义异常添加到现有项目，以模拟真实的异常。 如果在“停止调试”(Shift+F5) 之前应用仍在 Visual Studio 中运行

1. 在“解决方案资源管理器”中展开“页面” > “About.cshtml”，打开“About.cshtml.cs”。

   ![Visual Studio 解决方案资源管理器的屏幕截图](./media/app-insights-asp-net-core/011-about.png)

2. 在 ``Message=`` 下面添加一个异常，然后保存对该文件所做的更改。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>实时流

若要使用 ASP.NET Core 访问 Application Insights 的实时流功能，请更新到 **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet 包。

在 Visual Studio 中，选择“项目” > “管理 NuGet 包” > “Microsoft.ApplicationInsights.AspNetCore”>“版本 **2.2.0**” > “更新”。

  ![NuGet 包管理器的屏幕截图](./media/app-insights-asp-net-core/012-nuget-update.png)

会出现多次确认提示。 请阅读提示，并确认是否同意更改。

### <a name="page-view-load-time"></a>页面视图加载时间

1. 在 Visual Studio 中，导航到“解决方案资源管理器” > “页面”。需要修改两个文件：__Layout.cshtml_ 和 __ViewImports.cshtml_

2. 在 __ViewImports.cshtml_ 中添加：

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. 在 **_Layout.cshtml** 中的 ``</head>`` 标记前面添加以下行，而且该行必须添加在其他任何脚本的前面。

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>测试“失败的请求数”、“页面视图加载时间”和“实时流”

若要测试并确认一切正常，请执行以下操作：

1. 单击 IIS Express 运行应用 ![Visual Studio 中 IIS Express 图标的屏幕截图](./media/app-insights-asp-net-core/0012-iis-express.png)

2. 导航到“关于”页，触发测试异常。 （如果在“调试”模式下运行，则需在 Visual Studio 中单击“继续”，然后 Application Insights 中就会显示该异常。）

3. 重新运行前面所述的模拟 PowerShell 事务脚本（可能需要调整脚本中的端口号。）

4. 如果“Applications Insights 概述”仍未打开，请在 Visual Studio 菜单中选择“项目” > “Application Insights” > “打开 Applications Insights 门户”。 

   > [!TIP]
   > 如果仍未看到新流量，请检查“时间范围”并单击“刷新”。

   ![“概述”窗口的屏幕截图](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. 选择“实时流”

   ![“实时指标流”的屏幕截图](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   （如果 PowerShell 脚本仍在运行，则应会看到实时指标；如果该脚本已停止，请在打开“实时流”的情况下再次运行该脚本。）

## <a name="app-insights-sdk-comparison"></a>Application Insights SDK 的比较

Application Insights 产品小组一直在努力使[完整版 .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) 与 .Net Core SDK 的功能接近。 适用于 Application Insights 的 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) 版本 2.2.0 已经基本填补了两者的功能差距。

了解 [.NET 与 .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) 之间的差异与利弊。

   | SDK 比较 | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **实时指标**      | **+** |**-** | **+** |
   | **服务器遥测通道** | **+** |**-** | **+**|
   |**自适应采样**| **+** | **-** | **+**|
   | **SQL 依赖项调用**     | **+** |**-** | **+**|
   | **性能计数器*** | **+** | **-**| **-**|

在此上下文中，_性能计数器_是指[服务器端性能计数器](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters)，例如处理器、内存和磁盘利用率。

## <a name="open-source-sdk"></a>开源 SDK
[阅读代码或为其做出贡献](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Application Insights 搜索（继续）

若要更好地了解如何在用于 ASP.NET Core 2 项目的 Visual Studio 中使用 Application Insights 搜索（即使尚未显式安装 Application Insights NuGet 包）， 可查看“调试输出”。

如果在输出中搜索 _insight_ 一词，则会突出显示相关结果，如下所示：

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR 正在加载两个程序集： 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

Application Insights 遥测的每个实例中的 _unconfigured_ 表示此应用程序未与任何 ikey 关联，因此应用程序运行时生成的数据不会发送到 Azure，仅供本地搜索和分析之用。

部分原因是，NuGet 包 _Microsoft.AspNetCore.All_ 使用 [_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) 作为依赖项

![适用于 Microsoft.AspNETCore.all 的 NuGet 依赖项关系图的屏幕截图](./media/app-insights-asp-net-core/013-dependency.png)

在 Visual Studio 之外，如果你是在 VSCode 或某个其他的编辑器中编辑 ASP.NET Core 项目，则在未向项目显式添加 Application Insights 的情况下，这些程序集不会在调试过程中自动加载。

但在 Visual Studio 中，可以通过 [IHostingStartup 界面](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1)（可以在调试过程中动态添加 Application Insights）从外部程序集以这种方式启用本地 Application Insights 功能。

详细了解如何通过 [ASP.NET Core 中带 IHostingStartup 的外部程序集](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1)来增强应用。 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>如何在 Visual Studio .NET Core 项目中禁用 Application Insights

虽然自动启动 Application Insights 搜索功能对某些人来说可能很有用，但如果意外出现生成的调试遥测数据，可能会造成混淆。

如果只需禁用遥测生成，则可将以下代码块添加到 _Startup.cs_ 文件的 Configure 方法中：

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR 仍会加载 _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ 和 _Microsoft.ApplicationInsights.AspNetCore.dll_，但它们不会执行任何操作。

若要在 Visual Studio .NET Core 项目中完全禁用 Application Insights，则首选方法是选择“工具” > “选项” > “项目和解决方案” > “Web 项目”，然后勾选相应的框来禁用适用于 ASP.NET Core Web 项目的本地 Application Insights。 此功能已在 Visual Studio 15.6 中添加。

![Visual Studio 选项的 Window Web 项目屏幕的屏幕截图](./media/app-insights-asp-net-core/014-disable.png)

如果运行的是较早版本的 Visual Studio，需要彻底删除通过 IHostingStartup 加载的所有程序集，则可添加：

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

到 _Program.cs_：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                .UseStartup<Startup>()
                .Build();
    }
}
```

也可添加 ``"ASPNETCORE_preventHostingStartup": "True"`` 到 _launchSettings.json_ 环境变量。

使用这其中的任一方法时的问题是，它不仅禁用 Application Insights，而且会禁用 Visual Studio 中利用 IHostingStartup 启动功能的任何东西。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>后续步骤
* [浏览用户流](app-insights-usage-flows.md)，了解用户如何在应用中导航。
* [配置快照收集](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)，以便在引发异常时了解源代码和变量的状态。
* [使用 API](app-insights-api-custom-events-metrics.md)，发送自己的事件和指标以获取应用的性能和使用情况的更详细视图。
* [可用性测试](app-insights-monitor-web-app-availability.md)从世界各地不断检查应用。
