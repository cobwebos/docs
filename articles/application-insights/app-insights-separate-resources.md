---
title: "在 Azure Application Insights 中监视开发、测试和产品 | Microsoft Docs"
description: "在开发的不同阶段监视应用程序的性能和使用情况"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 43fb1e764c929be14d42c3d214b051aeb5367d77
ms.contentlocale: zh-cn
ms.lasthandoff: 03/15/2017


---
# <a name="separating-application-insights-resources"></a>分隔 Application Insights 资源
来自应用程序的不同组件和版本的遥测应该发送到不同的 Application Insights 资源，还是组合到一个资源中？ 本文介绍最佳做法和必要的技术。

首先，让我们了解问题。 从应用程序收到的数据由 Application Insights 在 Microsoft Azure *资源*中进行存储和处理。 每个资源都由*检测密钥* (iKey) 标识。 在应用中，此密钥提供给 Application Insights SDK，以便它将收集的数据发送到正确的资源。 该密钥可以在代码中或 ApplicationInsights.config 中提供。 通过在 SDK 中更改密钥，可以将数据定向到不同的资源。 

在简单的案例中，将应用程序注册到 Application Insights 时，将在 Application Insights 中创建新资源。 在 Visual Studio 中，“配置 Application Insights”或“添加 Application Insights”对话框将为你执行此操作。

如果它是大容量网站，则可能在多个服务器实例上部署它。

在较复杂的方案中，系统由多个组件组成，例如一个网站和一个后端处理器。 

## <a name="when-to-use-separate-ikeys"></a>何时使用单独的 iKey
下面是一些一般准则：

* 如果你有可单独部署的应用程序单元，并且该单元在一组可独立于其他组件放大/缩小的服务器实例上运行，则通常将该单元映射到单个资源，即，它将具有单个检测密钥 (iKey)。
* 与此相反，使用单独 iKey 的原因包括：
  * 从单独的组件轻松读取单独的指标。
  * 使小容量遥测独立于大容量，以便对一个流的限制、配置和采样不会影响其他流。
  * 单独的警报、导出和工作项配置。
  * 扩散遥测配额、限制和 Web 测试计数之类的[限制](app-insights-pricing.md#limits-summary)。
  * 正在开发和测试的代码应发送到单独的 iKey，而不是生产标记。  

许多 Application Insights 门户体验在设计时考虑了这些准则。 例如，服务器视图按服务器实例分段，从而假设关于单个逻辑组件的遥测可能来自多个服务器实例。

## <a name="single-ikey"></a>单个 iKey
如果将来自多个组件的遥测发送到单个 iKey：

* 向所有遥测添加属性，该属性允许对组件标识进行分段和筛选。 角色 ID 从服务器角色实例自动添加到遥测，但在其他情况下可使用[遥测初始值设定项](app-insights-api-filtering-sampling.md#add-properties)添加该属性。
* 同时更新不同组件中的 Application Insights SDK。 单个 iKey 的遥测应源自同一版本的 SDK。

## <a name="separate-ikeys"></a>单独 iKey
如果对不同的应用程序组件具有多个 iKey：

* 为逻辑应用程序中的关键遥测视图创建[仪表板](app-insights-dashboards.md)，从不同的应用程序组件组合而成。 仪表板可以共享，因此单个逻辑系统视图可供不同团队使用。
* 在团队级别上组织[资源组](app-insights-resources-roles-access-control.md)。 访问权限按资源组分配，其中包括设置警报的权限。 
* 使用 [Azure Resource Manager 模板和 Powershell](app-insights-powershell.md) 帮助管理警报规则和 Web 测试之类的项目。

## <a name="separate-ikeys-for-devtest-and-production"></a>用于开发/测试和生产的单独 iKey
若要使在发布应用时自动更改密钥更轻松，请在代码中（而不是在 ApplicationInsights.config 中）设置 iKey。

（如果系统是 Azure 云服务，有[另一种方法可以设置单独 ikey](app-insights-cloudservices.md)。）

### <a name="dynamic-ikey"></a> 动态检测密钥
在初始化方法中设置密钥，如 ASP.NET 服务中的 global.aspx.cs：

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

在此示例中，不同资源的 ikey 放置在 Web 配置文件的不同版本中。 通过交换 Web 配置文件（可作为发布脚本的一部分执行），将交换目标资源。

### <a name="web-pages"></a>网页
通过[从快速启动边栏选项卡获取的脚本](app-insights-javascript.md)，iKey 也在应用的网页中使用。 从服务器状态生成它，而不是逐字将其编码到脚本中。 例如，在 ASP.NET 应用中：

*使用 Razor 的 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="creating-an-additional-application-insights-resource"></a>创建其他 Application Insights 资源
如果你决定为不同的应用程序组件或同一组件的不同标记（开发/测试/生产）分隔遥测，则必须创建新的 Application Insights 资源。

在 [portal.azure.com](https://portal.azure.com) 中，添加 Application Insights 资源：

![依次单击“新建”、“Application Insights”](./media/app-insights-separate-resources/01-new.png)

* **应用程序类型**会影响在概述边栏选项卡上看到的内容和[指标资源管理器](app-insights-metrics-explorer.md)中的可用属性。 如果未看到应用类型，请选择网页的 Web 类型之一。
* **资源组**便于管理[访问控件](app-insights-resources-roles-access-control.md)之类的属性。 可为开发、测试和生产使用单独的资源组。
* **订阅**是 Azure 中的付款帐户。
* **位置**是保留数据的位置。 当前无法更改它。 
* **添加到仪表板**将资源的快速访问磁贴放在 Azure 主页上。 

创建资源需要几秒钟。 完成后，你将看到警报。

（可编写 [PowerShell 脚本](app-insights-powershell-script-create-resource.md)，自动创建资源。）

## <a name="getting-the-instrumentation-key"></a>获取检测密钥
检测密钥标识所创建的资源。 

![单击“Essentials”、单击“检测密钥，然后按“CTRL+C”](./media/app-insights-separate-resources/02-props.png)

需要将向其发送数据的所有资源的检测密钥。


