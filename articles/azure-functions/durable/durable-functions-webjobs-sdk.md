---
title: 如何以 WebJobs 的形式运行 Durable Functions - Azure
description: 了解如何使用 WebJobs SDK 编写 Durable Functions 的代码，并将其配置为在 WebJobs 中运行。
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: ad498bdbc4e6dc9745c6ef45b3dc601ad36c0a62
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733407"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>如何以 WebJobs 的形式运行 Durable Functions

默认情况下，Durable Functions 使用 Azure Functions 运行时来托管业务流程。 但是，在某些情况下，可能需要对侦听事件的代码进行更多的控制。 本文介绍如何使用 WebJobs SDK 来实现业务流程。 若要查看函数和 Web 作业之间更详细的比较，请参阅[比较函数和 Web 作业](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。

[Azure Functions](../functions-overview.md) 和 [Durable Functions](durable-functions-overview.md) 扩展构建在 [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) 基础之上。 WebJobs SDK 中的作业主机是 Azure Functions 中的运行时。 如果需要以 Azure Functions 中做不到的方式来控制行为，可以使用 WebJobs SDK 自行开发并运行 Durable Functions。

在 WebJobs SDK 的版本 3.x 中，主机是 `IHost` 的实现，而在版本 2.x 中，你使用 `JobHost` 对象。

链接 Durable Functions 示例在 Web 作业 SDK 2.x 版本中提供：下载或克隆[Durable Functions 存储库](https://github.com/azure/azure-functions-durable-extension/)，并查看*v1*分支，并中转到*\\samples webjobssdk\\链接*文件夹。

## <a name="prerequisites"></a>先决条件

本文假定读者熟悉 WebJobs SDK、Azure Functions C# 类库开发和 Durable Functions 的基础知识。 如需这些主题的简介，请参阅以下资源：

* [WebJobs SDK 入门](../../app-service/webjobs-sdk-get-started.md)
* [使用 Visual Studio 创建你的第一个函数](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

完成本文中的步骤：

* [安装 Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/)和**Azure 开发**工作负荷。

  如果已有 Visual Studio，但没有该工作负荷，请选择 "**工具** > " "**获取工具和功能**" 来添加工作负荷。

  可以改用 [Visual Studio Code](https://code.visualstudio.com/)，但某些说明仅适用于 Visual Studio。）

* 安装并运行 [Azure 存储模拟器](../../storage/common/storage-use-emulator.md) 5.2 版或更高版本。 替代方法是使用 Azure 存储连接字符串更新 *App.config* 文件。

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

本文介绍如何开发 WebJobs SDK 2.x 项目（等效于 Azure Functions 版本 1.x）。 有关版本 3.x 的信息，请参阅本文稍后的 [WebJobs SDK 3.x](#webjobs-sdk-3x)。

## <a name="create-a-console-app"></a>创建控制台应用

若要将 Durable Functions 作为 WebJobs 运行，必须先创建控制台应用。 WebJobs SDK 项目只是一个装有相应 NuGet 包的控制台应用项目。

在 Visual Studio 的“新建项目”对话框中，选择“Windows 经典桌面” > “控制台应用(.NET Framework)”。************ 在项目文件中，`TargetFrameworkVersion` 应为 `v4.6.1`。

Visual Studio 还具有 web 作业项目模板，可通过选择**云** > **Azure WebJob （.NET Framework）** 来使用。 此模板会安装许多的包，其中一些包可能并不需要。

## <a name="install-nuget-packages"></a>安装 NuGet 包

需要 WebJobs SDK 的 NuGet 包、核心绑定、日志记录框架和 Durable Task 扩展。 下面是这些包的“包管理器控制台”命令，并提供了截至编写本文时的最新稳定版本号：****

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

还需要日志记录提供程序。 以下命令将安装 Azure 应用程序 Insights 提供程序和`ConfigurationManager`。 使用 `ConfigurationManager` 可从应用设置中获取 Application Insights 检测密钥。

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

以下命令安装控制台提供程序：

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost 代码

创建控制台应用并安装所需的 NuGet 包以后，即可使用 Durable Functions。 可以使用 JobHost 代码来这样做。

若要使用 Durable Functions 扩展，请对 `Main` 方法中的 `JobHostConfiguration` 对象调用 `UseDurableTask`：

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

有关可在 `DurableTaskExtension` 对象中设置的属性列表，请参阅 [host.json](../functions-host-json.md#durabletask)。

`Main` 方法也是设置日志记录提供程序的位置。 下面的示例配置控制台和 Application Insights 提供程序。

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

## <a name="functions"></a>函数

WebJobs 上下文中的 Durable Functions 有点不同于 Azure Functions 上下文中的 Durable Functions。 在编写代码时，必须了解相关差异。

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

在 WebJobs SDK 项目中，可对业务流程客户端对象调用方法，而无需发送 HTTP 请求。 以下方法对应于可以使用 HTTP 管理 API 执行的三个任务：

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

示例项目中的业务流程客户端函数启动业务流程协调程序函数，然后进入每隔 2 秒调用 `GetStatusAsync` 的循环：

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

你已经将 Durable Functions 设置为以 WebJob 方式运行，并且已了解其与以独立 Azure Functions 形式运行 Durable Functions 时的区别。 此时可以在示例中查看其运行情况。

本部分概述如何运行[示例项目](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining)。 有关如何在本地运行 WebJobs SDK 项目并将其部署到 Azure WebJob 的详细说明，请参阅 [WebJobs SDK 入门](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)。

### <a name="run-locally"></a>在本地运行

1. 确保存储模拟器正在运行（参阅[先决条件](#prerequisites)）。

1. 如果要在本地运行项目时查看 Application Insights 中的日志，请执行以下操作：

    a. 创建一个 Application Insights 资源，并为其使用**一般**应用类型。

    b. 在 *App.config* 文件中保存检测密钥。

1. 运行该项目。

### <a name="run-in-azure"></a>在 Azure 中运行

1. 创建 Web 应用和存储帐户。

1. 在 web 应用中，将存储连接字符串保存到名为`AzureWebJobsStorage`的应用设置中。

1. 创建一个 Application Insights 资源，并为其使用**一般**应用类型。

1. 将检测密钥保存到名为`APPINSIGHTS_INSTRUMENTATIONKEY`的应用设置中。

1. 部署为 WebJob

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

本文介绍如何开发 Web 作业 SDK 2.x 项目。 如果要开发[Web 作业 SDK](../../app-service/webjobs-sdk-get-started.md) 2.x 项目，本部分将帮助你了解这些差异。

引入的主要变化是使用 .NET Core 而不是 .NET Framework。 若要创建 Web 作业 SDK 2.x 项目，说明是相同的，但有以下例外：

1. 创建 .NET Core 控制台应用。 在 Visual Studio 的 "**新建项目**" 对话框中，选择 " **.net core** > **控制台应用（.net core）**"。 项目文件指定 `TargetFramework` 为 `netcoreapp2.x`。

1. 选择以下包的 release 版本 Web 作业 SDK 2.x：

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. 使用 .NET Core 配置框架，在*appsettings*文件中设置存储连接字符串和 Application Insights 检测密钥。 下面是一个示例：

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. 更改`Main`方法代码以执行此操作。 下面是一个示例：

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
