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
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: 2245fcdaa8b7e85ea37e9af9c939cd188c4d7ed9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32157136"
---
# <a name="application-insights-for-aspnet-core"></a>用于 ASP.NET Core 的 Application Insights

Azure Application Insights 提供 Web 应用程序的监视信息，深度可达代码级别。 使用它可以轻松监视 Web 应用程序的可用性、性能和使用情况。 还可以快速确定并诊断应用程序中的错误，而无需等待用户报告这些错误。

本文逐步讲解如何在 Visual Studio 中创建一个 ASP.NET Core [Razor 页面](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio)示例应用程序，以及如何开始使用 Azure Application Insights 进行监视。

## <a name="prerequisites"></a>先决条件

- .NET Core 2.0.0 SDK 或更高版本。
- 装有 ASP.NET 和 Web 开发工作负荷的 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 版本 15.3 或以上。

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>在 Visual Studio 中创建 ASP.NET Core 项目

1. 单击右键，并管理员身份启动 **Visual Studio 2017**。
2. 选择“文件” > “新建” > “项目”(Ctrl-Shift-N)。

   ![Visual Studio 中“文件”>“新建”>“项目”菜单的屏幕截图](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. 展开“Visual C#”，选择“.NET Core” > “ASP.NET Core Web 应用程序”。 输入“名称” > “解决方案名称”，选中“创建新的 Git 存储库”。

   ![Visual Studio 中“文件”>“新建”>“项目”向导的屏幕截图](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. 选择“.Net Core” > “ASP.NET Core 2.0 Web 应用程序” > “确定”。

    ![Visual Studio 中“文件”>“新建”>“项目”选择菜单的屏幕截图](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>添加 Application Insights 遥测

1. 选择“项目” > “添加 Application Insights 遥测...”（或者，可以右键单击“连接的服务”并选择“添加连接的服务”。）

    ![Visual Studio 中“文件”>“新建”>“项目”选择菜单的屏幕截图](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. 选择“免费开始”。

    ![Visual Studio 中“文件”>“新建”>“项目”选择菜单的屏幕截图](./media/app-insights-asp-net-core/0005-start-free.png)

3. 选择相应的“订阅” > “资源”，指定是否允许每月收集超过 1 GB 数据，选择“注册”。

    ![Visual Studio 中“文件”>“新建”>“项目”选择菜单的屏幕截图](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>对项目所做的更改

Application Insights 的系统开销很低。 通过添加 Application Insights 遥测来查看对项目所做的修改：

选择“视图” > “团队资源管理器”(Ctrl+\, Ctrl+M) >“项目” > “更改”

- 共有四项更改：

  ![通过添加 Application Insights 更改文件的屏幕截图](./media/app-insights-asp-net-core/0007-changes.png)

- 创建了一个新文件：

   **ConnectedService.json**

  ![通过添加 Application Insights 更改文件的屏幕截图](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- 修改了三个文件：

  **appsettings.json**

   ![通过添加 Application Insights 更改文件的屏幕截图](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![通过添加 Application Insights 更改文件的屏幕截图](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![通过添加 Application Insights 更改文件的屏幕截图](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>使用 PowerShell 创建综合事务

启动应用，然后手动单击各个链接来生成测试流量。 但是，在 PowerShell 中创建简单的综合事务通常很有帮助。

1. 单击 IIS Express 运行应用 ![Visual Studio 中 IIS Express 图标的屏幕截图](./media/app-insights-asp-net-core/0012-iis-express.png)

2. 复制浏览器地址栏中的 URL。 它采用以下格式：http://localhost:{random 端口号}

   ![浏览器 URL 地址栏的屏幕截图](./media/app-insights-asp-net-core/0013-copy-url.png)

3. 运行以下 PowerShell 循环，针对测试应用创建 100 个综合事务。 修改 **localhost:** 后面的端口号，使之与上一步骤中复制的 URL 相匹配。

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>打开 Application Insights 门户

运行上一部分所述的 PowerShell 后，启动 Application Insights 查看事务并确认正在收集数据。 

在 Visual Studio 菜单中，选择“项目” > “Application Insights” > “打开 Application Insights 门户”

   ![Application Insights 概述的屏幕截图](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> 在上面的示例屏幕截图中，目前尚未收集“实时流”、“页面视图加载时间”和“失败的请求数”。 下一部分将逐步讲解如何添加每项设置。 如果已开始收集“实时流”和“页面视图加载时间”，则只需遵循“失败的请求数”的步骤。

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>收集“失败的请求数”、“实时流”和“页面视图加载时间”

### <a name="failed-requests"></a>失败的请求数

从技术上讲，系统已开始收集“失败的请求数”，但目前并未发生失败的请求。 为了加速该过程，可将一个自定义异常添加到现有项目，以模拟真实的异常。 如果在“停止调试”(Shift+F5) 之前应用仍在 Visual Studio 中运行

1. 在“解决方案资源管理器”中展开“页面” > “About.cshtml”，打开“About.cshtml.cs”。

   ![Visual Studio 解决方案资源管理器的屏幕截图](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. 在 ``Message=`` 下面添加一个异常，然后保存对该文件所做的更改。

   ```C#
   throw new Exception("Test Exception");
   ```

   ![异常代码的屏幕截图](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>实时流

若要使用 ASP.NET Core 访问 Application Insights 的实时流功能，请更新到 **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet 包。

在 Visual Studio 中，选择“项目” > “管理 NuGet 包” > “Microsoft.ApplicationInsights.AspNetCore”>“版本 **2.2.0**” > “更新”。

  ![NuGet 包管理器的屏幕截图](./media/app-insights-asp-net-core/0017-update-nuget.png)

系统会显示多条确认提示，请阅读提示，并确认是否同意更改。

### <a name="page-view-load-time"></a>页面视图加载时间

1. 在 Visual Studio 中，导航到“解决方案资源管理器” > “页面”。需要修改两个文件：**_Layout.cshtml** 和 **_ViewImports.cshtml**

2. 在 **_ViewImports.cshtml** 中添加：

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![_ViewImports.cshtml 中的代码更改屏幕截图](./media/app-insights-asp-net-core/00018-view-imports.png)

3. 在 **_Layout.cshtml** 中的 ``</head>`` 标记前面添加以下行。该行必须添加在其他任何脚本的前面。

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![layout.cshtml 中的代码更改屏幕截图](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>测试“失败的请求数”、“页面视图加载时间”和“实时流”

完成前面的步骤后，可以测试并确认一切是否正常。

1. 单击 IIS Express 运行应用 ![Visual Studio 中 IIS Express 图标的屏幕截图](./media/app-insights-asp-net-core/0012-iis-express.png)

2. 导航到“关于”页，触发测试异常。 （如果在调试模式下运行，则需要在 Visual Studio 中单击“继续”，然后，Application Insights 才会拾取该异常。）

3. 重新运行前面所述的模拟 PowerShell 事务脚本（可能需要调整脚本中的端口号。）

4. 如果“Applications Insights 概述”仍未打开，请在 Visual Studio 菜单中选择“项目” > “Application Insights” > “打开 Applications Insights 门户”。 

   > [!TIP]
   > 如果仍未看到新流量，请检查“时间范围”并单击“刷新”。

   ![“概述”窗口的屏幕截图](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. 选择“实时流”

   ![“实时指标流”的屏幕截图](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   （如果 PowerShell 脚本仍在运行，则应会看到实时指标；如果该脚本已停止，请在打开“实时流”的情况下再次运行该脚本。）

## <a name="app-insights-sdk-comparison"></a>Application Insights SDK 的比较

Application Insights 产品小组一直在努力使[完整版 .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) 与 .Net Core SDK 的功能尽量接近。 适用于 Application Insights 的 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) 版本 2.2.0 已经基本填补了两者的功能差距。

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

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>后续步骤
* [浏览用户流](app-insights-usage-flows.md)，了解用户如何在应用中导航。
* [配置快照收集](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)，以便在引发异常时了解源代码和变量的状态。
* [使用 API](app-insights-api-custom-events-metrics.md)，发送自己的事件和指标以获取应用的性能和使用情况的更详细视图。
* [可用性测试](app-insights-monitor-web-app-availability.md)从世界各地不断检查应用。
