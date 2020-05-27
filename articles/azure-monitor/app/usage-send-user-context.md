---
title: 用于跟踪活动的用户上下文 ID - Azure Application Insights
description: 在 Application Insights 中通过向每位用户分配一个唯一的永久性 ID（字符串），跟踪其如何使用服务。
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: ecdcc8a84cdccb05ec514003d63f808583d719c9
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797684"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>发送用户上下文 ID 以启用 Azure Application Insights 中的使用体验

## <a name="tracking-users"></a>跟踪用户

Application Insights 通过一套产品使用工具监视并跟踪用户：

- [用户、会话、事件](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [漏斗图](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [保留](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)队列
- [工作簿](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)

为了跟踪用户在各个时间进行的操作，Application Insights 需要每个用户或会话的 ID。 在每个自定义事件或页面视图中包含以下 ID。

- 用户、漏斗图、保留和队列：包括用户 ID。
- 会话：包括会话 ID。

> [!NOTE]
> 这是一篇高级文章，概述了使用 Application Insights 跟踪用户活动的手动步骤。 对于许多 Web 应用程序，**可能不需要这些步骤**，因为默认的服务器端 SDK 与[客户端/浏览器端 JavaScript SDK](../../azure-monitor/app/website-monitoring.md ) 结合使用，通常足以自动跟踪用户活动。 如果除了服务器端 SDK 之外还没有配置[客户端监视](../../azure-monitor/app/website-monitoring.md )，请先执行该操作并测试用户行为分析工具是否按预期执行。

## <a name="choosing-user-ids"></a>选择用户 ID

应在各用户会话中保留用户 ID，以便跟踪各个时间的用户行为。 可通过多种方法保留 ID。

- 服务中已有的用户定义。
- 如果服务有权访问浏览器，那么它可以向浏览器传递包含 ID 的 cookie。 只要 cookie 保留在用户的浏览器中，ID 将会保留。
- 如有必要，每个会话都可以使用一个新 ID，但是会限制有关用户的结果。 例如，不能查看用户各个时间的行为变化。

ID 必须是 GUID 或另一个复杂程度足以唯一地标识每个用户的字符串。 例如，可能是一长串随机数。

如果 ID 包含用户的个人标识信息，则不适合将该值作为用户 ID 发送到 Application Insights。 可以将该 ID 作为[已经过身份验证的用户 ID](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users) 进行发送，但是这不满足使用方案的用户 ID 要求。

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET 应用：在 ITelemetryInitializer 中设置用户上下文

创建遥测初始化程序，详见[此处](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer)。 通过请求遥测传递会话 ID，并设置 Context.User.Id 和 Context.Session.Id。

此示例将用户 ID 设置为在会话后过期的标识符。 如果可能，请使用在各会话中保留的用户 ID。

### <a name="telemetry-initializer"></a>遥测初始化程序

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

- 若要启用使用体验，请首先发送[自定义事件](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)或[页面视图](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [使用情况概述](usage-overview.md)
    - [用户、会话和事件](usage-segmentation.md)
    - [漏斗图](usage-funnels.md)
    - [保留](usage-retention.md)
    - [工作簿](../../azure-monitor/platform/workbooks-overview.md)
