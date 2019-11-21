---
title: How to run Durable Functions as WebJobs - Azure
description: 了解如何使用 WebJobs SDK 编写 Durable Functions 的代码，并将其配置为在 WebJobs 中运行。
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232745"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>How to run Durable Functions as WebJobs

By default, Durable Functions uses the Azure Functions runtime to host orchestrations. However, there may be certain scenarios where you need more control over the code that listens for events. This article shows you how to implement your orchestration using the WebJobs SDK. To see a more detailed comparison between Functions and WebJobs, see [Compare Functions and WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) 和 [Durable Functions](durable-functions-overview.md) 扩展构建在 [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) 基础之上。 The job host in the WebJobs SDK is the runtime in Azure Functions. If you need to control behavior in ways not possible in Azure Functions, you can develop and run Durable Functions by using the WebJobs SDK yourself.

In version 3.x of the WebJobs SDK, the host is an implementation of `IHost`, and in version 2.x you use the `JobHost` object.

The chaining Durable Functions sample is available in a WebJobs SDK 2.x version: download or clone the [Durable Functions repository](https://github.com/azure/azure-functions-durable-extension/), and go to the *samples\\webjobssdk\\chaining* folder.

## <a name="prerequisites"></a>必备组件

本文假定读者熟悉 WebJobs SDK、Azure Functions C# 类库开发和 Durable Functions 的基础知识。 如需这些主题的简介，请参阅以下资源：

* [WebJobs SDK 入门](../../app-service/webjobs-sdk-get-started.md)
* [使用 Visual Studio 创建你的第一个函数](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

完成本文中的步骤：

* [Install Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) with the **Azure development** workload.

  If you already have Visual Studio, but don't have that workload, add the workload by selecting **Tools** > **Get Tools and Features**.

  可以改用 [Visual Studio Code](https://code.visualstudio.com/)，但某些说明仅适用于 Visual Studio。）

* 安装并运行 [Azure 存储模拟器](../../storage/common/storage-use-emulator.md) 5.2 版或更高版本。 替代方法是使用 Azure 存储连接字符串更新 *App.config* 文件。

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

本文介绍如何开发 WebJobs SDK 2.x 项目（等效于 Azure Functions 版本 1.x）。 有关版本 3.x 的信息，请参阅本文稍后的 [WebJobs SDK 3.x](#webjobs-sdk-3x)。

## <a name="create-a-console-app"></a>创建控制台应用

To run Durable Functions as WebJobs, you must first create a console app. WebJobs SDK 项目只是一个装有相应 NuGet 包的控制台应用项目。

In the Visual Studio **New Project** dialog box, select **Windows Classic Desktop** > **Console App (.NET Framework)** . 在项目文件中，`TargetFrameworkVersion` 应为 `v4.6.1`。

Visual Studio also has a WebJob project template, which you can use by selecting **Cloud** > **Azure WebJob (.NET Framework)** . 此模板会安装许多的包，其中一些包可能并不需要。

## <a name="install-nuget-packages"></a>安装 NuGet 包

需要 WebJobs SDK 的 NuGet 包、核心绑定、日志记录框架和 Durable Task 扩展。 Here are **Package Manager Console** commands for those packages, with the latest stable version numbers as of the date this article was written:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

还需要日志记录提供程序。 The following commands install the Azure Application Insights provider and the `ConfigurationManager`. 使用 `ConfigurationManager` 可从应用设置中获取 Application Insights 检测密钥。

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

以下命令安装控制台提供程序：

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost 代码

Having created the console app and installed the NuGet packages you need, you're ready to use Durable Functions. You do so by using JobHost code.

若要使用 Durable Functions 扩展，请对 `Main` 方法中的 `JobHostConfiguration` 对象调用 `UseDurableTask`：

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

有关可在 `DurableTaskExtension` 对象中设置的属性列表，请参阅 [host.json](../functions-host-json.md#durabletask)。

`Main` 方法也是设置日志记录提供程序的位置。 The following example configures the console and Application Insights providers.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

Durable Functions in the context of WebJobs differs somewhat from Durable Functions in the context of Azure Functions. It's important to be aware of the differences as you write your code.

WebJobs SDK 不支持以下 Azure Functions 功能：

* [FunctionName 属性](#functionname-attribute)
* [HTTP 触发器](#http-trigger)
* [Durable Functions HTTP 管理 API](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName 属性

在 WebJobs SDK 项目中，函数的方法名称是函数名称。 `FunctionName` 属性只能在 Azure Functions 中使用。

### <a name="http-trigger"></a>HTTP 触发器

WebJobs SDK 没有 HTTP 触发器。 示例项目的业务流程客户端使用计时器触发器：

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP 管理 API

由于没有 HTTP 触发器，WebJobs SDK 没有 [HTTP 管理 API](durable-functions-http-api.md)。

In a WebJobs SDK project, you can call methods on the orchestration client object, instead of by sending HTTP requests. 以下方法对应于可以使用 HTTP 管理 API 执行的三个任务：

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

The orchestration client function in the sample project starts the orchestrator function, and then goes into a loop that calls `GetStatusAsync` every 2 seconds:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>运行示例

You've got Durable Functions set up to run as a WebJob, and you now have an understanding of how this will differ from running Durable Functions as standalone Azure Functions. At this point, seeing it work in a sample might be helpful.

本部分概述如何运行[示例项目](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining)。 有关如何在本地运行 WebJobs SDK 项目并将其部署到 Azure WebJob 的详细说明，请参阅 [WebJobs SDK 入门](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)。

### <a name="run-locally"></a>在本地运行

1. 确保存储模拟器正在运行（参阅[先决条件](#prerequisites)）。

1. If you want to see logs in Application Insights when you run the project locally:

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 Create an Application Insights resource, and use the **General** app type for it.

    b. 在 *App.config* 文件中保存检测密钥。

1. 运行该项目。

### <a name="run-in-azure"></a>在 Azure 中运行

1. 创建 Web 应用和存储帐户。

1. In the web app, save the storage connection string in an app setting named `AzureWebJobsStorage`.

1. Create an Application Insights resource, and use the **General** app type for it.

1. Save the instrumentation key in an app setting named `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. 部署为 WebJob

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

This article explains how to develop a WebJobs SDK 2.x project. If you're developing a [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) project, this section helps you understand the differences.

The main change introduced is the use of .NET Core instead of .NET Framework. To create a WebJobs SDK 3.x project, the instructions are the same, with these exceptions:

1. 创建 .NET Core 控制台应用。 In the Visual Studio **New Project** dialog box, select  **.NET Core** > **Console App (.NET Core)** . 项目文件指定 `TargetFramework` 为 `netcoreapp2.x`。

1. Choose the release version WebJobs SDK 3.x of the following packages:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Set the storage connection string and the Application Insights instrumentation key in an *appsettings.json* file, by using the .NET Core configuration framework. 下面是一个示例：

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Change the `Main` method code to do this. 下面是一个示例：

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>后续步骤

若要详细了解 WebJobs SDK，请参阅[如何使用 WebJobs SDK](../../app-service/webjobs-sdk-how-to.md)。
