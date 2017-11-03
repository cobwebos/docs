---
title: "使用 Azure Application Insights 诊断 Web 应用中的故障和异常 | Microsoft Docs"
description: "从 ASP.NET 应用中捕获异常以及请求遥测。"
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: cb87b166a32c47395f99c9cd59442a7ccd65b7ed
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>使用 Application Insights 诊断 Web 应用中的异常
[Application Insights](app-insights-overview.md) 可报告实时 Web 应用中的异常。 可以将失败的请求与异常关联到客户端和服务器上的其他事件，从而快速诊断原因。

## <a name="set-up-exception-reporting"></a>设置异常报告
* 若要报告服务器应用中的异常，请执行以下操作：
  * 在应用代码中安装 [Application Insights SDK](app-insights-asp-net.md)，或者
  * 在 IIS Web 服务器上运行 [Application Insights 代理](app-insights-monitor-performance-live-website-now.md)，或者
  * 在 Azure Web 应用中添加 [Application Insights 扩展](app-insights-azure-web-apps.md)
  * Java Web 应用：安装 [Java 代理](app-insights-java-agent.md)
* 在网页中安装 [JavaScript 代码片段](app-insights-javascript.md)可以捕获浏览器异常。
* 在某些应用程序框架中或者使用某些设置时，需要执行一些额外的步骤来捕获异常：
  * [Web 窗体](#web-forms)
  * [MVC](#mvc)
  * [Web API 1*](#web-api-1x)
  * [Web API 2*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>使用 Visual Studio 诊断异常
在 Visual Studio 中打开应用解决方案，帮助进行调试。

使用 F5 在服务器或开发计算机上运行应用。

在 Visual Studio 中打开“Application Insights 搜索”窗口，然后将它设置为显示应用中的事件。 进行调试时，只需单击 Application Insights 按钮即可执行此操作。

![右键单击项目，并依次选择“Application Insights”、“打开”。](./media/app-insights-asp-net-exceptions/34.png)

请注意，可以筛选报告，以便仅显示异常。

*没有显示异常？请参阅[捕获异常](#exceptions)。*

单击异常报告，显示器堆栈跟踪。
单击堆栈跟踪中的行引用，打开相关代码文件。  

请注意，CodeLens 会在代码中显示有关异常的数据：

![有关异常的 CodeLens 通知。](./media/app-insights-asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>使用 Azure 门户诊断故障
Application Insights 附带了精选的 APM 体验，帮助你诊断所监视应用程序中的失败。 若要开始，请单击位于“调查”部分中的 Application Insights 资源菜单中的“失败”选项。 此时应看到一个显示请求的失败率趋势的全屏视图，其中包括多少个请求将要失败以及多少个用户受到影响。 在右侧将看到一些最有用的特定于所选失败操作的分发，包括前 3 个响应代码、前 3 个异常类型以及前 3 种失败依赖项类型。 

![失败会审视图（“操作”选项卡）](./media/app-insights-asp-net-exceptions/FailuresTriageView.png)

只需单击一下，即可查看其中每个子集的操作具有代表性的示例。 具体而言，若要诊断异常，可以单击要在“异常详细信息”边栏选项卡中显示的特定异常的计数，如下所示：

![“异常详细信息”边栏选项卡](./media/app-insights-asp-net-exceptions/ExceptionDetailsBlade.png)

**或者，**可以切换到“异常”选项卡，从异常的总体视图开始，而不是查看特定失败操作的异常：

![失败会审视图（“异常”选项卡）](./media/app-insights-asp-net-exceptions/FailuresTriageView_Exceptions.png)

这里可以看到为所监视的应用收集的所有异常。

*没有显示异常？请参阅[捕获异常](#exceptions)。*


## <a name="custom-tracing-and-log-data"></a>自定义跟踪和日志数据
要获取特定于应用的诊断数据，可在插入代码后发送自己的遥测数据。 该数据与请求、页面视图和其他自动收集的数据一起显示在诊断搜索中。

有几种选项：

* [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) 通常用于监视使用模式，但它发送的数据还显示在诊断搜索的“自定义事件”下。 事件可以进行命名，并带有[筛选诊断搜索](app-insights-diagnostic-search.md)所依据的字符串属性和数值指标。
* [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) 允许发送较长的数据，例如 POST 信息。
* [TrackException()](#exceptions) 可发送堆栈跟踪。 [有关异常的详细信息](#exceptions)。
* 如果已使用 Log4Net 或 NLog 等记录框架，可以[捕获这些日志](app-insights-asp-net-trace-logs.md)并在诊断搜索中查看它们以及请求和异常数据。

要查看这些事件，请打开 [搜索](app-insights-diagnostic-search.md)、打开“筛选器”，并选择“自定义事件”、“跟踪”或“异常”。

![深入了解](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> 如果应用生成大量遥测，自适应采样模块将通过仅发送具有代表性的事件部分自动减少发送到门户的量。 将以组为单位选择或取消选择属于同一操作的事件，以便可以在相关事件之间浏览。 [了解采样](app-insights-sampling.md)。
>
>

### <a name="how-to-see-request-post-data"></a>如何查看请求 POST 数据
请求详细信息不包含在 POST 调用中发送到应用的数据。 若要报告此数据：

* 在应用程序中[安装 SDK](app-insights-asp-net.md)。
* 在应用程序中插入代码以调用 [Microsoft.ApplicationInsights.TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace)。 在消息参数中发送 POST 数据。 允许的大小有限制，因此，应该尝试仅发送必要数据。
* 调查失败的请求时，查找关联的跟踪。  

![深入了解](./media/app-insights-asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a>捕获异常和相关的诊断数据
首先，不会在门户中看到在应用中导致失败的所有异常。 将显示所有浏览器异常（如果在网页中使用 [JavaScript SDK](app-insights-javascript.md)）， 但大多数服务器异常由 IIS 导致，必须编写几行代码才能看到它们。

可以：

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

如果网页包括内容交付网络或其他域中的脚本文件，确保脚本标记具有属性 ```crossorigin="anonymous"```，并且服务器可发送 [CORS 标头](http://enable-cors.org/)。 这允许从这些资源中获取有关未处理的 JavaScript 异常的堆栈跟踪和详细信息。

## <a name="web-forms"></a>Web 窗体
在 Web 窗体中，当不存在通过 CustomErrors 配置的重定向时，HTTP 模块能够收集异常

但是，如果有活动重定向，在 Global.asax.cs 中将以下行添加到 Application_Error 函数。 （如果还没有活动重定向，则添加 Global.asax 文件）。

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
将 HandleError 属性替换为控制器中的新属性。

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

作为替代项，可以：

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

打开“指标资源管理器”边栏选项卡、添加新图表，并选择在“性能计数器”下列出的“异常率”。

.NET Framework 通过对间隔中的异常数进行计数并除以间隔长度计算异常率。

这与 Application Insights 门户通过对 TrackException 报告计数计算得出的“异常”计数不同。 采样间隔不同，SDK 不会为所有经处理和未经处理的异常发送 TrackException 报告。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="next-steps"></a>后续步骤
* [监视 REST、SQL 以及其他对依赖项的调用](app-insights-asp-net-dependencies.md)
* [监视器页面加载时间、浏览器异常和 AJAX 调用](app-insights-javascript.md)
* [监视性能计数器](app-insights-performance-counters.md)
