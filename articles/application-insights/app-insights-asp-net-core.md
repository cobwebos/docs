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
ms.openlocfilehash: a0e0d2c3604ea2357f52c3d464933c622ba1810d
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523234"
---
# <a name="application-insights-for-aspnet-core"></a>用于 ASP.NET Core 的 Application Insights

Azure Application Insights 提供 Web 应用程序的监视信息，深度可达代码级别。 使用它可以轻松监视 Web 应用程序的可用性、性能和使用情况。 还可以快速确定并诊断应用程序中的错误，而无需等待用户报告这些错误。

本文逐步讲解如何在 Visual Studio 中创建示例 ASP.NET Core [应用程序的步骤](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio)。 还介绍了如何使用 Application Insights 启动监视。

## <a name="prerequisites"></a>先决条件

- .NET Core 2.0.0 SDK 或更高版本
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7.3 版本或更高版本，带有 ASP.NET 和 Web 开发工作负载

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>在 Visual Studio 中创建 ASP.NET Core 项目

1. 右键单击“Visual Studio 2017”，然后选择“以管理员身份运行”。
2. 选择“文件”、“新建”、“项目”(Ctrl+Shift+N) >  > 。

   ![Visual Studio“新建项目”菜单的屏幕截图](./media/app-insights-asp-net-core/001-new-project.png)

3. 展开“Visual C#”。 选择“.NET Core” > “ASP.NET Core Web 应用程序”。 输入项目名称和解决方案名称，然后选择“创建新 Git 存储库”。

   ![Visual Studio“新建项目向导”的屏幕截图](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. 选择“.NET Core” > “ASP.NET Core 2.0 Web 应用程序” > “确定”。

    ![Visual Studio“新建项目”对话框的屏幕截图](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights 搜索

在 Visual Studio 2015 Update 2 或更高版本中，如果其中包含基于 ASP.NET Core 2+ 的项目，则可充分利用 [Application Insights 搜索](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)，即使在向项目显式添加 Application Insights 之前，也可以这样做。

要测试此功能，请执行以下操作：

1. 运行应用。 要运行应用，请选择“IIS Express”图标（![Visual Studio 中 IIS Express 图标的屏幕截图](./media/app-insights-asp-net-core/004-iis-express.png)）。

2. 选择“视图” > “其他窗口” > “Application Insights 搜索”。

   ![Visual Studio 诊断工具选择的屏幕截图](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. 调试会话遥测目前仅适用于本地分析。 要完全启用 Application Insights，请选择右上角的“遥测准备情况”，或完成下一章节中列出的步骤。

   ![Visual Studio Application Insights 搜索的屏幕截图](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> 要详细了解 Visual Studio 如何在你向 ASP.NET Core 项目添加 Application Insights 之前在本地启用 [Application Insights 搜索](app-insights-visual-studio.md)和 [CodeLens ](app-insights-visual-studio-codelens.md) 等功能的详细信息，请参阅 [Application Insights 搜索（续）](#application-insights-search-continued)。

## <a name="add-application-insights-telemetry"></a>添加 Application Insights 遥测

1. 选择“项目” > “添加 Application Insights 遥测”。 （或右键单击“连接服务”，然后再单击“添加连接服务”）。

    ![Visual Studio 新建项目选择菜单的屏幕截图](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. 选择“入门”。 （根据 Visual Studio 版本的不同，文本可能会稍有不同。 某些较旧的版本具有“免费开始”按钮。）

    ![Application Insights 入门按钮的屏幕截图](./media/app-insights-asp-net-core/008-get-started.png)

3. 选择订阅，然后选择“资源” > “注册”。

## <a name="changes-made-to-your-project"></a>对项目所做的更改

Application Insights 的系统开销低。 通过添加 Application Insights 遥测来查看对项目所做的修改：

选择“视图” > “团队资源管理器”(Ctrl+\, Ctrl+M) >“项目” > “更改”

- 总共更改了四处：

  ![通过添加 Application Insights 更改文件的屏幕截图](./media/app-insights-asp-net-core/009-changes.png)

- 创建了一个新文件：

  -  _ConnectedService.json_

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "8.12.10405.1",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```

- 修改了三个文件（添加了附加注释以突出显示更改）：

  - appsettings.json：

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

  - ContosoDotNetCore.csproj：

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

  -  Program.cs：

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

通过综合事务自动向应用发起请求：：

1. 要运行应用，请选择 ![Visual Studio 中 IIS Express 图标的屏幕截图](./media/app-insights-asp-net-core/004-iis-express.png) 图标。

2. 复制浏览器地址栏中的 URL。 该 URL 的格式为 `http://localhost:<port number>`。

   ![地址栏中浏览器 URL 的屏幕截图](./media/app-insights-asp-net-core/0013-copy-url.png)

3. 运行以下 PowerShell 循环，使用测试应用创建 100 个综合事务。 修改 `localhost:` 后的端口号，以匹配在上一步中复制的 URl。 例如：

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>打开 Application Insights 门户

运行上一部分所述的 PowerShell 后，启动 Application Insights 查看事务并确认正在收集数据。 

在 Visual Studio 菜单中，请选择“项目” > “Application Insights” > “打开 Application Insights 门户”。

   ![Application Insights 概述的屏幕截图](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> 在上面的示例屏幕截图中，目前尚未收集“实时流”、“页面视图加载时间”和“失败的请求数”。 下一节将引导你完成添加其中每项功能的步骤。 如果已开始收集“实时流”和“页面视图加载时间”，则只需对“失败的请求数”完成相关步骤。

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>收集“失败的请求数”、“实时流”和“页面视图加载时间”

### <a name="failed-requests"></a>失败的请求数

从技术上讲，系统已开始收集“失败的请求数”，但目前并未发生失败的请求。 要加速此过程，可将一个自定义异常添加到现有项目，以模拟真实的异常。 如果在继续加速之前应用仍在 Visual Studio 中运行，请选择“停止调试”(Shift+F5)。

1. 在“解决方案资源管理器”中展开“页面” > “Pages About.cshtml”，然后打开“About.cshtml.cs”。

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

要使用 ASP.NET Core 访问 Application Insights 的实时流功能，请更新 Microsoft.ApplicationInsights.AspNetCore 2.2.0 NuGet 包。

在 Visual Studio 中，请选择“项目” > “管理 NuGet 包” > “Microsoft.ApplicationInsights.AspNetCore”>（版本）2.2.0  > “更新”。

  ![NuGet 包管理器的屏幕截图](./media/app-insights-asp-net-core/012-nuget-update.png)

出现多个确认提示。 请阅读提示，并确认是否同意更改。

### <a name="page-view-load-time"></a>页面视图加载时间

1. 在 Visual Studio 中，转到“解决方案资源管理器” > “页面”。 必须修改两个文件：Layout.cshtml 和 ViewImports.cshtml。

2. 在 ViewImports.cshtml 中，添加此代码：

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. 在 Layout.cshtml 中，在 ``</head>`` 标记和其他任何脚本之前添加以下代码：

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>测试失败的请求数、页面视图加载时间和实时流

要测试并确认一切正常，请执行以下操作：

1. 运行应用。 要运行应用，请选择 ![Visual Studio 中 IIS Express 图标的屏幕截图](./media/app-insights-asp-net-core/004-iis-express.png) 图标。

2. 转到“关于”页，触发测试异常。 （如果是调试模式，则在 Visual Studio 中选择“继续”，然后 Application Insights 中就会显示该异常。）

3. 重新运行之前使用的模拟的 PowerShell 事务脚本。 （可能需要调整脚本中的端口号。）

4. 如果 Applications Insights 中的“概述”页面仍未打开，请在 Visual Studio 菜单中选择“项目” > “Application Insights” > “打开 Applications Insights 门户”。 

   > [!TIP]
   > 如果看不到新流量，请检查“时间范围”的值，然后选择“刷新”。

   ![概览窗口的屏幕截图](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. 选择“实时流”。

   ![“实时指标流”的屏幕截图](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   （如果 PowerShell 脚本仍在运行，应看到实时指标。 （如果 PowerShell 脚本已停止运行，请在实时流打开的情况下再次运行该脚本。）

## <a name="application-insights-sdk-comparison"></a>Application Insights SDK 对比

Application Insights 产品组一直在努力实现[完整版 .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) 与 .Net Core SDK 之间的功能奇偶一致性。 适用于 Application Insights 的 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) 的 2.2.0 版本已经基本填补了两者的功能差距。

下表描述了 [.NET 和 .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) 之间的更多差异和权衡：

   | SDK 对比 | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **实时指标**      | **+** |**-** | **+** |
   | **服务器遥测通道** | **+** |**-** | **+**|
   |**自适应采样**| **+** | **-** | **+**|
   | **SQL 依赖项调用**     | **+** |**-** | **+**|
   | **性能计数器*** | **+** | **-**| **-**|

在此上下文中，性能计数器是指[服务器端性能计数器](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters)，例如处理器、内存和磁盘利用率。

## <a name="open-source-sdk"></a>开源 SDK
[阅读代码或为其做出贡献](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Application Insights 搜索（续）

本章节可帮助用户更好地了解 Application Insights 搜索在 Visual Studio 中如何运行 ASP.NET Core 2 项目。 即使尚未安装 Application Insights NuGet 包，工作原理也是如此。 也可帮助检查调试输出。

如果在输出中搜索“insight”一词，则会突出显示以下内容结果：

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

在输出中，CoreCLR 加载两个程序集： 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

Application Insights 遥测每个实例中引用的“未配置”均表示此应用程序未与 ikey 关联。 应用运行时生成的数据不会发送到 Azure。 此数据仅适用于本地搜索和分析。

该功能的部分原因是，NuGet 包 Microsoft.AspNetCore.All 使用 [_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) 作为依赖项。

![适用于 Microsoft.AspNETCore.all 的 NuGet 依赖项关系图的屏幕截图](./media/app-insights-asp-net-core/013-dependency.png)

在 Visual Studio 之外，如果你是在 VSCode 或其他编辑器中编辑 ASP.NET Core 项目，则在未向项目显式添加 Application Insights 的情况下，这些程序集不会在调试过程中自动加载。

但在 Visual Studio 中，可以通过使用 [IHostingStartup 接口](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1)从外部程序集启用本地 Application Insights 功能。 接口会在调试期间动态添加 Application Insights。

详细了解如何通过 [ASP.NET Core 中带 IHostingStartup 的外部程序集](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1)来增强应用。 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>在 Visual Studio .NET Core 项目中禁用 Application Insights

虽然自动启动 Application Insights 搜索功能可能很有用，但如果意外出现生成的调试遥测数据，可能会造成混淆。

如果只需禁用遥测生成，则可将此代码块添加到 Startup.cs 文件的 Configure 方法中：

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR 仍会加载 Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll 和 Microsoft.ApplicationInsights.AspNetCore.dll，但这些文件不会执行任何操作。

若要在 Visual Studio .NET Core 项目中完全禁用 Application Insights，则首选方法是选择“工具” > “选项” > “项目和解决方案” > “Web 项目”。 选择“禁用 ASP.NET Core Web 项目的本地 Application Insights”复选框。 此功能已在 Visual Studio 15.6 中添加。

![Visual Studio 选项的 Window Web 项目屏幕的屏幕截图](./media/app-insights-asp-net-core/014-disable.png)

如果运行较早版本的 Visual Studio，并且想要彻底删除通过 IHostingStartup 加载的所有程序集，则有两种选择：

* 将 `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` 添加到 Program.cs：

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

* 也可将 ``"ASPNETCORE_preventHostingStartup": "True"`` 添加到 launchSettings.json 环境变量。

使用这两种方法都存在一个问题，即它们不会只禁用 Application Insights。 这些方法还会禁用使用 IHostingStartup 启用功能的 Visual Studio 中的任何内容。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>后续步骤
* [浏览用户流](app-insights-usage-flows.md)，了解用户如何在应用中导航。
* [配置快照收集](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)，以便在引发异常时查看源代码和变量的状态。
* [使用 API](app-insights-api-custom-events-metrics.md)，发送自己的事件和指标以获取应用的性能和使用情况的更详细视图。
* 使用[可用性测试](app-insights-monitor-web-app-availability.md)从世界各地不断检查应用。
