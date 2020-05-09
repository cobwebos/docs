---
title: 使用 Azure Functions 创建和运行自定义可用性测试
description: 本文档将介绍如何创建包含 TrackAvailability （）的 Azure 函数，该函数将根据 TimerTrigger 函数中给定的配置定期运行。 此测试的结果将发送到 Application Insights 资源，你可以在其中查询可用性结果数据并对其发出警报。 自定义测试使你可以编写比使用门户 UI 更复杂的可用性测试、监视 Azure VNET 内的应用、更改终结点地址，或者在你的区域中不可用时创建可用性测试。
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/04/2020
ms.openlocfilehash: 81040adf6cfbb8820ec7f306c7d614830e3a2613
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791097"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>使用 Azure Functions 创建和运行自定义可用性测试

本文介绍如何使用 TrackAvailability （）创建 Azure 函数，该函数将根据 TimerTrigger 函数中给定的配置定期运行，并使用自己的业务逻辑。 此测试的结果将发送到 Application Insights 资源，你可以在其中查询可用性结果数据并对其发出警报。 这允许您创建类似于通过门户中的[可用性监视](../../azure-monitor/app/monitor-web-app-availability.md)来实现的自定义测试。 自定义的测试允许编写比使用门户 UI 更复杂的可用性测试、监视 Azure VNET 内的应用、更改终结点地址，或者即使此功能在你的区域中不可用，也可以创建可用性测试。

> [!NOTE]
> 此示例只是为了向您展示 TrackAvailability （） API 调用在 Azure 函数中的工作原理的机制。 不要编写将其转换为功能完备的可用性测试所需的基础 HTTP 测试代码/业务逻辑。 默认情况下，如果你逐步完成此示例，你将创建一个始终会生成失败的可用性测试。

## <a name="create-timer-triggered-function"></a>创建计时器触发的函数

- 如果有 Application Insights 资源：
    - 默认情况下 Azure Functions 创建 Application Insights 资源，但如果想要使用已创建的某个资源，则需在创建过程中指定。
    - 按照以下选项操作，了解如何[创建 Azure Functions 资源和计时器触发的函数](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)（在清理之前停止）。
        -  选择顶部附近的 "**监视**" 选项卡。

            ![ 使用自己的 App Insights 资源创建 Azure Functions 应用](media/availability-azure-functions/create-function-app.png)

        - 选择 "Application Insights" 下拉框，然后键入或选择资源的名称。

            ![选择现有 Application Insights 资源](media/availability-azure-functions/app-insights-resource.png)

        - 选择 "**查看 + 创建**"
- 如果尚未为计时器触发的函数创建 Application Insights 资源：
    - 默认情况下，在创建 Azure Functions 应用程序时，它将为你创建一个 Application Insights 资源。
    - 按照有关如何[创建 Azure Functions 资源和计时器触发函数](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)（在清理之前停止）的说明进行操作。

## <a name="sample-code"></a>示例代码

将下面的代码复制到 run.csx 文件中（这将替换预先存在的代码）。 为此，请进入 Azure Functions 应用程序，并选择左侧的计时器触发器函数。

>[!div class="mx-imgBorder"]
>![Azure 门户中的 Azure 函数的 run.csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> 对于要使用的终结点地址： `EndpointAddress= https://dc.services.visualstudio.com/v2/track`。 除非你的资源位于 Azure 政府版或 Azure 中国等区域，否则请参阅此文，了解如何[覆盖默认终结点](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification)，并选择适用于你所在区域的相应遥测通道终结点。

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

在右侧的 "查看文件" 下，选择 "**添加**"。 调用具有以下配置的新文件**function。**

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![在右侧选择 "添加"。 将文件命名为 proj](media/availability-azure-functions/addfile.png)

在右侧的 "查看文件" 下，选择 "**添加**"。 将新文件 RunAvailabilityTest 与以下配置一起调用 **。**

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>检查可用性

若要确保一切正常，可以在 Application Insights 资源的 "可用性" 选项卡中查看图形。

> [!NOTE]
> 如果你在 runAvailabilityTest 中实现自己的业务逻辑，则会看到如以下屏幕截图中所示的成功结果，如果你没有这样做，则会看到失败的结果。 用`TrackAvailability()`创建的测试将在测试名称旁显示为 "**自定义**"。

>[!div class="mx-imgBorder"]
>![成功结果的 "可用性" 选项卡](media/availability-azure-functions/availability-custom.png)

若要查看端到端事务详细信息，请在 "深化到" 下选择 "**成功**" 或 "**失败**"，然后选择一个示例。 还可以通过选择关系图上的数据点来访问端到端事务详细信息。

>[!div class="mx-imgBorder"]
>![选择示例可用性测试](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![端到端事务详细信息](media/availability-azure-functions/end-to-end.png)

如果按原样运行所有内容（不添加业务逻辑），则会看到测试已失败。

## <a name="query-in-logs-analytics"></a>在日志中查询（Analytics）

你可以使用日志（分析）来查看可用性结果、依赖关系等。 若要了解有关日志的详细信息，请访问[日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

>[!div class="mx-imgBorder"]
>![可用性结果](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![依赖项](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>后续步骤

- [应用程序地图](../../azure-monitor/app/app-map.md)
- [事务诊断](../../azure-monitor/app/transaction-diagnostics.md)
