---
title: 如何进行 Durable Functions 以 WebJobs-Azure
description: 了解如何使用 WebJobs SDK 编写 Durable Functions 的代码，并将其配置为在 WebJobs 中运行。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: e8473ece2ed08798836dc66067e1ce042924f469
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431249"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>如何为 web 作业运行 Durable Functions

默认情况下，Durable Functions 使用 Azure Functions 运行时与主机的业务流程。 但是，可能有某些情况下，在你需要加强控制侦听的事件的代码。 本文介绍如何实现您的业务流程使用 WebJobs SDK。 若要查看函数和 WebJobs 之间的更详细的比较，请参阅[比较函数和 WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。

[Azure Functions](../functions-overview.md) 和 [Durable Functions](durable-functions-overview.md) 扩展构建在 [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) 基础之上。 在 WebJobs SDK 中的作业主机是在 Azure Functions 中的运行时。 如果需要控制不能在 Azure Functions 中的方式的行为，您可以开发和运行您自己使用 WebJobs SDK 的 Durable Functions。

在版本 3.x 中的 WebJobs SDK，该主机是实现`IHost`，和版本 2.x 使用`JobHost`对象。

在 WebJobs SDK 2.x 版本提供了该链接的 Durable Functions 示例： 下载或克隆[Durable Functions 存储库](https://github.com/azure/azure-functions-durable-extension/)，并转到*示例\\webjobssdk\\链接*文件夹。

## <a name="prerequisites"></a>必备组件

本文假定读者熟悉 WebJobs SDK、Azure Functions C# 类库开发和 Durable Functions 的基础知识。 如需这些主题的简介，请参阅以下资源：

* [WebJobs SDK 入门](../../app-service/webjobs-sdk-get-started.md)
* [使用 Visual Studio 创建你的第一个函数](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

完成本文中的步骤：

* [安装包含 **Azure 开发**工作负荷的 Visual Studio 2017 版本 15.6 或以上](https://docs.microsoft.com/visualstudio/install/)。

  如果你已有 Visual Studio 中，但没有该工作负荷，通过选择添加工作负荷**工具** > **获取工具和功能**。

  可以改用 [Visual Studio Code](https://code.visualstudio.com/)，但某些说明仅适用于 Visual Studio。）

* 安装并运行 [Azure 存储模拟器](../../storage/common/storage-use-emulator.md) 5.2 版或更高版本。 替代方法是使用 Azure 存储连接字符串更新 *App.config* 文件。

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

本文介绍如何开发 WebJobs SDK 2.x 项目（等效于 Azure Functions 版本 1.x）。 有关版本 3.x 的信息，请参阅本文稍后的 [WebJobs SDK 3.x](#webjobs-sdk-3x)。

## <a name="create-a-console-app"></a>创建控制台应用

若要为 web 作业运行 Durable Functions，必须先创建一个控制台应用程序。 WebJobs SDK 项目只是一个装有相应 NuGet 包的控制台应用项目。

在 Visual Studio**新的项目**对话框中，选择**Windows 经典桌面** > **控制台应用 (.NET Framework)**。 在项目文件中，`TargetFrameworkVersion` 应为 `v4.6.1`。

Visual Studio 还具有 web 作业项目模板，您可以通过选择使用该**云** > **Azure web 作业 (.NET Framework)**。 此模板会安装许多的包，其中一些包可能并不需要。

## <a name="install-nuget-packages"></a>安装 NuGet 包

需要 WebJobs SDK 的 NuGet 包、核心绑定、日志记录框架和 Durable Task 扩展。 以下是**程序包管理器控制台**这些包，截至撰写本文时的最新稳定版号的命令：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

还需要日志记录提供程序。 以下命令安装 Azure Application Insights 提供程序和`ConfigurationManager`。 使用 `ConfigurationManager` 可从应用设置中获取 Application Insights 检测密钥。

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

以下命令安装控制台提供程序：

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost 代码

具有创建控制台应用程序和安装的 NuGet 包，您需要你已准备好使用 Durable Functions。 您使用 JobHost 代码执行此操作。

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

在 web 作业的上下文中的持久函数不同于 Durable Functions 的某种程度上 Azure Functions 的上下文中。 务必要注意的差异编写代码。

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

在 WebJobs SDK 项目中，您可以发送 HTTP 请求而不是调用业务流程客户端对象上的方法。 以下方法对应于可以使用 HTTP 管理 API 执行的三个任务：

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

示例项目中的业务流程客户端函数启动业务流程协调程序函数，然后进入一个循环，调用`GetStatusAsync`每隔 2 秒：

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

你有将设置为运行 web 作业，Durable Functions，并且现在已了解了的这将如何不同于运行用作独立的 Azure Functions 的 Durable Functions。 此时，监控其运行一个示例中可能非常有用。

本部分概述如何运行[示例项目](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining)。 有关如何在本地运行 WebJobs SDK 项目并将其部署到 Azure WebJob 的详细说明，请参阅 [WebJobs SDK 入门](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)。

### <a name="run-locally"></a>在本地运行

1. 确保存储模拟器正在运行（参阅[先决条件](#prerequisites)）。

1. 如果你想要在本地运行项目时，请参阅在 Application Insights 中的日志：

    a. 创建 Application Insights 资源，并用**常规**为它的应用类型。

    b. 在 *App.config* 文件中保存检测密钥。

1. 运行该项目。

### <a name="run-in-azure"></a>在 Azure 中运行

1. 创建 Web 应用和存储帐户。

1. 在 web 应用中，将保存存储连接字符串中的应用设置命名`AzureWebJobsStorage`。

1. 创建 Application Insights 资源，并用**常规**为它的应用类型。

1. 将检测密钥保存在某个应用程序设置命名`APPINSIGHTS_INSTRUMENTATIONKEY`。

1. 部署为 WebJob

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

本文介绍如何开发 WebJobs SDK 2.x 项目。 如果要开发 web 作业 SDK 3.x 项目，此部分可帮助您了解的差异。

引入的主要更改是使用.NET Core，而不是.NET Framework。 若要创建 web 作业 SDK 3.x 项目，说明是相同的但以下情况例外：

1. 创建 .NET Core 控制台应用。 在 Visual Studio**新的项目**对话框中，选择 **.NET Core** > **控制台应用 (.NET Core)**。 项目文件指定 `TargetFramework` 为 `netcoreapp2.0`。

1. 选择 WebJobs SDK 的预发行版本 3.x.版的以下包：

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. 获取存储连接字符串和中的 Application Insights 检测密钥*appsettings.json*文件中的，通过使用.NET Core 配置框架。 更改`Main`方法代码来执行此操作。 下面是一个示例：

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString =
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

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

## <a name="next-steps"></a>后续步骤

若要详细了解 WebJobs SDK，请参阅[如何使用 WebJobs SDK](../../app-service/webjobs-sdk-how-to.md)。
