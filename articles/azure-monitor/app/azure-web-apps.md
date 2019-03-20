---
title: 监视 Azure 应用服务性能 | Microsoft Docs
description: Azure 应用服务的应用程序性能监视。 对负载和响应时间、依赖项信息绘制图表，并对性能设置警报。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 92a7c1a45655f8804aa1f81b1a77ebf7cd5197e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122159"
---
# <a name="monitor-azure-app-service-performance"></a>监视 Azure 应用服务性能
在 [Azure 门户](https://portal.azure.com)中，可以为 [Azure 应用服务](../../app-service/overview.md)中的 Web 应用、移动后端和 API 应用设置应用程序性能监视。 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 将检测应用，将其活动的相关遥测数据发送到 Application Insights 服务，以便在其中存储和分析。 此处的指标图表和搜索工具可用于帮助诊断问题、改善性能以及评估使用情况。

## <a name="runtime-or-build-time"></a>运行时或生成时间
可以使用以下两种方法之一来配置通过检测应用进行监视：

* **运行时**-可以选择的性能监视扩展你的应用服务已激活。 无需重建或重装应用。 获取一组标准包用于监视响应时间、成功率、异常、依赖项等。

* **生成时** - 在开发期间，可以在应用中安装一个包。 此选项更灵活。 除了安装相同的标准包以外，还可以编写代码来自定义遥测，或发送自己的遥测数据。 可以根据应用域的语义记录特定的活动或记录事件。 这还使您能够测试 Application Insights SDK 的最新版本，如你可选择要评估测试版 Sdk，而运行时监视仅限于最新稳定版本。

## <a name="runtime-instrumentation-with-application-insights"></a>使用 Application Insights 的运行时检测
如果您当前在 Azure 中运行的应用服务，你已经获得了一些监视： 默认情况下请求和错误率。 添加 Application Insights 可获得更多功能，例如响应时间、 监视调用依赖项，智能检测，以及对功能强大的 Kusto 查询语言访问权限。 

1. 在应用服务的 Azure 控制面板中，选择“Application Insights”。

    ![在“设置”下选择“Application Insights”](./media/azure-web-apps/settings-app-insights.png)

   * 除非已为此应用设置了 Application Insights 资源，否则请选择创建新资源。 

     > [!NOTE]
     > 当单击“确定”来创建新资源时，将提示你**应用监视设置**。 选择“继续”会将新的 Application Insights 资源链接到你的应用服务，这样做还会**触发应用服务的重新启动**。 

     ![检测 Web 应用](./media/azure-web-apps/create-resource.png)

2. 指定要使用哪些资源后，可以选择 Application Insights 根据平台为应用程序收集数据的方式。 ASP.NET 应用程序监视是上： 默认情况下具有两个不同级别的集合。

    ![根据平台选择选项](./media/azure-web-apps/choose-options-new.png)

   * .NET 基本集合级别提供基本的单实例 APM 功能。
    
   * .NET 建议集合级别：
       * 添加 CPU、内存和 I/O 使用情况趋势。
       * 跨请求/依赖关系边界关联微服务。
       * 收集使用情况趋势，并启用从可用性结果到事务的关联。
       * 收集未经主机进程处理的异常。
       * 提高使用采样时，负载下的 APM 指标准确性。
    
     提供了.NET core**建议集合**或**禁用**.NET Core 2.0 和 2.1。

3. 安装 Application Insights 后，可以**检测应用服务**。

   为页面视图和用户遥测启用客户端监视。

    （对于使用建议集合的 .NET Core 应用，这在默认情况下会启用，无论是否存在应用设置“APPINSIGHTS_JAVASCRIPT_ENABLED”。 用于禁用客户端监视的基于精细 UI 的支持当前不可用于 .NET Core。）
    
   * 选择“设置”>“应用程序设置”
   * 在“应用设置”下添加新的键/值对：

     键：`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值： `true`
   * **保存**设置并**重新启动**应用。

4. 通过选择“设置” > “Application Insights” > “在 Application Insights 中查看更多”来浏览你的应用的监视数据。

以后，可以根据需要使用 Application Insights 生成应用。

*如何删除 Application Insights 或改为发送到另一个资源？*

* 在 Azure 中，打开 Web 应用控制边栏选项卡，并在“设置”下打开“Application Insights”。 可单击顶部的“禁用”关闭 Application Insights，或在“更改资源”部分中选择新资源。

## <a name="build-the-app-with-application-insights"></a>使用 Application Insights 生成应用
Application Insights 可以通过将 SDK 安装到应用中来提供更详细的遥测。 具体而言，可以收集跟踪日志，[编写自定义遥测](../../azure-monitor/app/api-custom-events-metrics.md)，以及获取更详细的异常报告。

1. **在 Visual Studio 中**（2013 Update 2 或更高版本），为项目配置 Application Insights。

    右键单击 Web 项目，并选择“添加”>“Application Insights”或“项目” > “Application Insights” > “配置 Application Insights”。

    ![右键单击 Web 项目，并选择“添加或配置 Application Insights”](./media/azure-web-apps/03-add.png)

    如果系统提示登录，请使用 Azure 帐户的凭据。

    该操作会产生两种效果：

   1. 在 Azure 中创建 Application Insights 资源，这是存储、分析和显示遥测数据的位置。
   2. 将 Application Insights NuGet 包添加到代码（如果该包尚不存在），并将该包配置为向 Azure 资源发送遥测数据。
2. 可以通过在开发计算机上运行应用来**测试遥测** (F5)。
3. 以普通的方式**将应用发布**到 Azure。 

*如何改为向不同的 Application Insights 资源发送数据？*

* 在 Visual Studio 中右键单击项目，选择“配置 Application Insights”，并选择所需的资源。 可以使用相应的选项来创建新资源。 重新生成并重新部署。

## <a name="automate-monitoring"></a>自动监视

若要启用使用 Application Insights 的遥测数据收集需要设置仅应用程序设置：

   ![应用服务应用程序设置的 Application Insights 的可用设置](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>应用程序设置定义

|应用设置名称 |  定义 | 值 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 主扩展，它控制运行时监视。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  在默认模式仅、 基本功能的支持是为了确保获得最佳性能。 | `default` 或`recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | 控制是否二进制重写引擎`InstrumentationEngine`将打开。 此设置会影响性能，并且会影响冷启动/启动时间。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | 如果 SQL 和 Azure 表的文本的控件将与依赖项调用一起捕获。 性能警告： 这需要`InstrumentationEngine`。 | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>应用服务应用程序设置使用 Azure 资源管理器

可以管理和使用配置的应用服务应用程序设置[Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。 部署新应用服务资源使用 Azure 资源管理器自动化或修改现有资源的设置时，可以使用此方法。

下面是应用服务的应用程序设置 JSON 的基本结构：

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

有关示例的 Azure 资源管理器模板使用应用程序设置配置了 Application Insights 的此[模板](https://github.com/Andrew-MSFT/BasicImageGallery)会非常有用，特别是在开始节[行 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)。

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>自动创建 Application Insights 资源并链接到新创建的应用服务。

若要配置的所有默认 Application Insights 设置都创建的 Azure 资源管理器模板，开始此过程，因为如果您将使用已启用 Application Insights 中都创建新的 Web 应用。

选择**自动化选项**

   ![应用服务 web 应用创建菜单](./media/azure-web-apps/create-web-app.png)

这将生成的最新的 Azure 资源管理器模板配置的所有必需设置。

  ![应用服务 web 应用模板](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> 在"默认"模式下，该模板将生成应用程序设置。 此模式下是性能优化，尽管可以修改模板以激活任何您喜欢的功能。

## <a name="more-telemetry"></a>其他遥测数据

* [网页加载数据](../../azure-monitor/app/javascript.md)
* [自定义遥测](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>故障排除

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>是否仍需要转到扩展-添加-新的应用服务应用的 Application Insights 扩展？

不，您不再需要手动添加该扩展。 通过设置边栏选项卡中启用 Application Insights 将添加所有所需应用程序设置来启用监视。 这是现在，因为以前添加的扩展插件的文件现[预装](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)作为应用服务映像的一部分。 文件位于`d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`。

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>如果运行时和生成时间监视同时启用了我的最终重复的数据？

否，默认情况下如果检测到生成时间监视，则运行时监视通过扩展将停止发送数据并将遵循的监视配置的生成时间。 确定是否禁用运行时监视基于检测的任何这三个文件：

* Microsoft.ApplicationInsights dll
* Microsoft.ASP.NET.TelemetryCorrelation dll
* System.Diagnostics.DiagnosticSource dll

请务必记住，在许多版本的 Visual Studio 中，部分或全部这些文件会默认添加到 ASP.NET 和 ASP.NET Core Visual Studio 模板文件。 如果你的项目创建基于模板之一，即使尚未显式启用了 Application Insights，文件依赖项存在会阻止运行时监视激活。

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED 导致 NET CORE Web 应用程序中出现不完整的 HTML 响应。

通过应用服务启用 Javascript 可能会导致 html 响应被截断。

* 解决方法 1：将 APPINSIGHTS_JAVASCRIPT_ENABLED 应用程序设置设为 false 或者完全将其删除并重启
* 解决方法 2： 将 sdk 通过代码添加和删除扩展 （Profiler 和快照调试器不会使用此配置）

若要跟踪此问题，请转到[导致不完整的 HTML 响应的 Azure 扩展](https://github.com/Microsoft/ApplicationInsights-Home/issues/277)。

对于 .NET Core，当前不支持以下内容：

* 独立部署。
* 面向 .NET Framework 的应用。
* .NET Core 2.2 应用程序。

> [!NOTE]
> 支持 .NET Core 2.0 和 .NET Core 2.1。 添加 .NET Core 2.2 支持时，会更新本文。

## <a name="next-steps"></a>后续步骤
* [在实时应用上运行探查器](../../azure-monitor/app/profiler.md)。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - 使用 Application Insights 监视 Azure Functions
* [将 Azure 诊断配置为](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)向 Application Insights 发送数据。
* [监视服务运行状况指标](../../azure-monitor/platform/data-collection.md)以确保服务可用且做出快速响应。
* 每当操作事件发生或指标超过阈值时[接收警报通知](../../azure-monitor/platform/alerts-overview.md)。
* 若要从访问网页的浏览器获取客户端遥测数据，请使用[适用于 JavaScript 应用和网页的 Application Insights](../../azure-monitor/app/javascript.md)。
* [设置可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)，以便在站点关闭时发出警报。