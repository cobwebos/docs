---
title: "使用 Azure Application Insights 诊断 Web 应用中的故障和异常 | Microsoft Docs | Microsoft Docs"
description: "从 ASP.NET 应用中捕获异常以及请求遥测。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 9a3df0ad2483471023ebb954d613bc5cad8fb7bf
ms.openlocfilehash: c4a20fe310d9a70bb3a954bd936daf6f3d432db9
ms.lasthandoff: 02/02/2017


---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>使用 Application Insights 诊断 Web 应用中的异常
[Application Insights](app-insights-overview.md) 可报告实时 Web 应用中的异常。 可以将失败的请求与异常关联到客户端和服务器上的其他事件，从而快速诊断原因。

## <a name="set-up-exception-reporting"></a>设置异常报告
* 若要报告服务器应用中的异常，请执行以下操作：
  * 在应用代码中安装 [Application Insights SDK](app-insights-asp-net.md)，或者
  * 在 IIS Web 服务器上运行 [Application Insights 代理](app-insights-monitor-performance-live-website-now.md)，或者
  * 在 Azure Web 应用中添加 [Application Insights 扩展](app-insights-azure-web-apps.md)
* 在网页中安装 [JavaScript 代码片段](app-insights-javascript.md)可以捕获浏览器异常。
* 在某些应用程序框架中或者使用某些设置时，需要执行一些额外的步骤来捕获异常：
  * [Web 窗体](#web-forms)
  * [MVC](#mvc)
  * [Web API 1*](#web-api-1)
  * [Web API 2*](#web-api-2)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>使用 Visual Studio 诊断异常
在 Visual Studio 中打开应用解决方案，帮助进行调试。

使用 F5 在服务器或开发计算机上运行应用。

在 Visual Studio 中打开“Application Insights 搜索”窗口，然后将它设置为显示应用中的事件。 进行调试时，只需单击 Application Insights 按钮即可执行此操作。

![右键单击项目，然后依次选择“Application Insights”、“打开”。](./media/app-insights-asp-net-exceptions/34.png)

请注意，你可以筛选报告，以便仅显示异常。

*没有显示异常？请参阅[捕获异常](#exceptions)。*

单击异常报告，显示器堆栈跟踪。

![单击异常。](./media/app-insights-asp-net-exceptions/35.png)

单击堆栈跟踪中的行引用，打开相关文件。  

## <a name="diagnosing-failures-using-the-azure-portal"></a>使用 Azure 门户诊断故障
在你的应用的 Application Insights 概述中，故障磁贴显示了异常和失败的 HTTP 请求图表，以及导致最常见故障的请求 URL 的列表。

![依次选择“设置”、“故障”](./media/app-insights-asp-net-exceptions/012-start.png)

单击列表中失败的请求类型之一，访问该故障的单独事件。 在此处，单击与之关联的异常或任何跟踪数据：

![选择失败请求的实例，然后在异常详细信息下，访问异常实例。](./media/app-insights-asp-net-exceptions/030-req-drill.png)

**或者，**可以从在“故障”边栏选项卡下进一步查找的异常列表开始。 保持单击，直到访问单独的异常。

![深入了解](./media/app-insights-asp-net-exceptions/040-exception-drill.png)

*没有显示异常？请参阅[捕获异常](#exceptions)。*

可在此处查看每个异常的堆栈跟踪和详细属性，并找到相关的日志跟踪或其他事件。

![深入了解](./media/app-insights-asp-net-exceptions/050-exception-properties.png)

[详细了解诊断搜索](app-insights-diagnostic-search.md)。

## <a name="custom-tracing-and-log-data"></a>自定义跟踪和日志数据
若要获取特定于你的应用的诊断数据，可在插入代码后发送你自己的遥测数据。 该数据与请求、页面视图和其他自动收集的数据一起显示在诊断搜索中。

你有几种选项：

* [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) 通常用于监视使用模式，但它发送的数据还显示在诊断搜索的“自定义事件”下。 事件可以进行命名，并带有[筛选诊断搜索](app-insights-diagnostic-search.md)所依据的字符串属性和数值指标。
* [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) 允许你发送较长的数据，例如 POST 信息。
* [TrackException()](#exceptions) 可发送堆栈跟踪。 [有关异常的详细信息](#exceptions)。
* 如果已使用 Log4Net 或 NLog 等记录框架，可以[捕获这些日志](app-insights-asp-net-trace-logs.md)并在诊断搜索中查看它们以及请求和异常数据。

若要查看这些事件，请打开 [搜索](app-insights-diagnostic-search.md)、打开“筛选器”，然后选择“自定义事件”、“跟踪”或“异常”。

![深入了解](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> 如果应用生成大量遥测，自适应采样模块将通过仅发送具有代表性的事件部分自动减少发送到门户的量。 将以组为单位选择或取消选择属于同一操作的事件，以便可以在相关事件之间浏览。 [了解采样](app-insights-sampling.md)。
>
>

### <a name="how-to-see-request-post-data"></a>如何查看请求 POST 数据
请求详细信息不包含在 POST 调用中发送到你的应用的数据。 若要报告此数据：

* 在应用程序中[安装 SDK](app-insights-asp-net.md)。
* 在应用程序中插入代码以调用 [Microsoft.ApplicationInsights.TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace)。 在消息参数中发送 POST 数据。 允许的大小有限制，因此你应该尝试仅发送必要数据。
* 调查失败的请求时，查找关联的跟踪。  

![深入了解](./media/app-insights-asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a>捕获异常和相关的诊断数据
首先，你不会在门户中看到在你的应用中导致失败的所有异常。 将显示所有浏览器异常（如果在网页中使用 [JavaScript SDK](app-insights-javascript.md)）， 但大多数服务器异常由 IIS 导致，你必须编写几行代码才能看到它们。

你可以：

* 通过在异常处理程序中插入代码来**显式记录异常**，从而报告这些异常。
* 通过配置 ASP.NET 框架**自动捕获异常**。 框架类型不同，必要的附加内容也不同。

## <a name="reporting-exceptions-explicitly"></a>显式报告异常
最简单的方法是在异常处理程序中插入对 TrackException() 的调用。

JavaScript

    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

属性和测量参数是可选的，但对于[筛选和添加](app-insights-diagnostic-search.md)额外信息很有用。 例如，如果有一个可运行多个游戏的应用，可查找与特定游戏相关的所有异常报告。 可向每个字典添加任意数量的项目。

## <a name="browser-exceptions"></a>浏览器异常
报告大多数浏览器异常。

如果你的网页包括内容交付网络或其他域中的脚本文件，确保你的脚本标记具有属性 ```crossorigin="anonymous"```，并且服务器可发送 [CORS 标头](http://enable-cors.org/)。 这允许你从这些资源中获取有关未处理的 JavaScript 异常的堆栈跟踪和详细信息。

## <a name="web-forms"></a>Web 窗体
在 Web 窗体中，当不存在通过 CustomErrors 配置的重定向时，HTTP 模块将能够收集异常

但是，如果你有活动重定向，在 Global.asax.cs 中将以下行添加到 Application_Error 函数。 （如果还没有活动重定向，则添加 Global.asax 文件）。

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## <a name="mvc"></a>MVC
如果 [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) 配置为 `Off`，[HTTP 模块](https://msdn.microsoft.com/library/ms178468.aspx)将可以收集异常。 但是，如果它是 `RemoteOnly`（默认值）或 `On`，异常将清除，并且 Application Insights 无法自动收集它。 可通过替换 [System.Web.Mvc.HandleErrorAttribute 类](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)并应用替换类修复该问题，如下面针对不同的 MVC 版本所示（[github 源](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)）：

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }

#### <a name="mvc-2"></a>MVC 2
将 HandleError 属性替换为你的控制器中的新属性。

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[示例](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
将 `AiHandleErrorAttribute` 注册为 Global.asax.cs 中的全局筛选器：

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[示例](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4、MVC5
将 AiHandleErrorAttribute 注册为 FilterConfig.cs 中的全局筛选器：

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[示例](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api-1x"></a>Web API 1.x
替换 System.Web.Http.Filters.ExceptionFilterAttribute：

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

可将此替换属性添加到特定控制器，或者将其添加到 WebApiConfig 类中的全局筛选器配置：

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[示例](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

存在大量无法处理异常筛选器的情况。 例如：

* 从控制器构造函数引发的异常。
* 从消息处理程序引发的异常。
* 在路由过程中引发的异常。
* 在响应内容序列化期间引发的异常。

## <a name="web-api-2x"></a>Web API 2.x
添加 IExceptionLogger 的实现：

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

将其添加到 WebApiConfig 中的服务：

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
  }

[示例](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

作为替代项，你可以：

1. 仅将 ExceptionHandler 替换为 IExceptionHandler 的自定义实现。 这仅在框架仍能够选择要发送哪个响应消息时调用（不会在终止实例连接时调用）
2. 异常筛选器（如上述 Web API 1.x 控制器上的部分中所述），并非在所有情况下都调用。

## <a name="wcf"></a>WCF
添加可扩展属性并实现 IErrorHandler 和 IServiceBehavior 的类。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

将属性添加到服务实现：

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...

[示例](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>异常性能计数器
如果在服务器上[安装了 Application Insights 代理](app-insights-monitor-performance-live-website-now.md)，可以获取 .NET 测量的异常率图表。 这包括经处理和未经处理的 .NET 异常。

打开“指标资源管理器”边栏选项卡、添加新图表，然后选择在“性能计数器”下列出的“异常率”。

.NET Framework 通过对间隔中的异常数进行计数并除以间隔长度计算异常率。

请注意，它与 Application Insights 通过计数 TrackException 报告计算得出的“异常”计数不同。 采样间隔不同，SDK 不会为所有经处理和未经处理的异常发送 TrackException 报告。

## <a name="next-steps"></a>后续步骤
* [监视 REST、SQL 以及对其他依赖项的调用](app-insights-asp-net-dependencies.md)
* [监视器页面加载时间、浏览器异常和 AJAX 调用](app-insights-javascript.md)
* [监视性能计数器](app-insights-performance-counters.md)

