---
title: 使用 Azure 函数创建和运行自定义可用性测试
description: 此文档将介绍如何使用 Track 可用性（）创建 Azure 函数，该函数将根据 TimerTrigger 函数中给出的配置定期运行。 此测试的结果将发送到您的应用程序见解资源，您可以在其中查询和警报可用性结果数据。 自定义测试将允许您编写比使用门户 UI 更复杂的可用性测试、监视 Azure VNET 内部的应用、更改终结点地址或创建可用性测试（如果区域不可用）。
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665793"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>使用 Azure 函数创建和运行自定义可用性测试

本文将介绍如何使用 Track 可用性（）创建 Azure 函数，该函数将根据 TimerTrigger 函数中提供的配置使用您自己的业务逻辑定期运行。 此测试的结果将发送到您的应用程序见解资源，您可以在其中查询和警报可用性结果数据。 这允许您创建自定义测试，类似于通过门户中[的可用性监视](../../azure-monitor/app/monitor-web-app-availability.md)可以执行的测试。 自定义测试将允许您编写比使用门户 UI 更复杂的可用性测试、监视 Azure VNET 内部的应用、更改终结点地址或创建可用性测试（即使此功能在您所在区域中不可用）。

> [!NOTE]
> 此示例仅用于向您展示 Track 可用性（） API 调用在 Azure 函数中的工作方式。 不是如何编写基础 HTTP 测试代码/业务逻辑，该逻辑需要将其转换为功能齐全的可用性测试。 默认情况下，如果您遍历此示例，您将创建一个可用性测试，该测试将始终生成失败。

## <a name="create-timer-triggered-function"></a>创建计时器触发函数

- 如果您有应用程序见解资源：
    - 默认情况下，Azure 函数创建应用程序见解资源，但如果要使用已创建的资源之一，则需要在创建期间指定该资源。
    - 按照以下选项，按照有关如何[创建 Azure 函数资源和计时器触发函数](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)（清理前停止）的说明进行操作。
        -  选择顶部附近的 **"监视"** 选项卡。

            ![ 使用自己的应用见解资源创建 Azure 函数应用](media/availability-azure-functions/create-function-app.png)

        - 选择"应用程序见解"下拉框，键入或选择资源的名称。

            ![选择现有的应用程序见解资源](media/availability-azure-functions/app-insights-resource.png)

        - 选择 **"审阅" 创建**
- 如果尚未为计时器触发函数创建应用程序见解资源：
    - 默认情况下，在创建 Azure 函数应用程序时，它将为您创建应用程序见解资源。
    - 按照有关如何[创建 Azure 函数资源和计时器触发函数](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)的说明（清理前停止）。

## <a name="sample-code"></a>示例代码

将下面的代码复制到 run.csx 文件中（这将替换预先存在的代码）。 为此，请进入 Azure 函数应用程序，然后选择左侧的计时器触发器函数。

>[!div class="mx-imgBorder"]
>![Azure 函数在 Azure 门户中的 run.csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> 对于要使用的终结点地址： `EndpointAddress= https://dc.services.visualstudio.com/v2/track`。 除非您的资源位于 Azure 政府或 Azure 中国这样的区域，在这种情况下，请参阅本文，[介绍重写默认终结点](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification)，并为区域选择相应的遥测通道终结点。

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

在视图文件右侧，选择 **"添加**"。 使用以下配置调用新的文件**函数.proj。**

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
>![在右侧选择中，添加。 命名文件函数.](media/availability-azure-functions/addfile.png)

在视图文件右侧，选择 **"添加**"。 使用以下配置调用新文件**run 可用性测试.csx。**

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>检查可用性

为了确保一切工作，您可以在应用程序见解资源的"可用性"选项卡中查看图形。

> [!NOTE]
> 如果您在 run 可用性Test.csx 中实现了自己的业务逻辑，那么您将看到成功的结果，如下面的屏幕截图所示，如果没有，则您将看到失败的结果。

>[!div class="mx-imgBorder"]
>![具有成功结果的可用性选项卡](media/availability-azure-functions/availtab.png)

使用 Azure 函数设置测试时，您会注意到，与在"可用性"选项卡中使用 **"添加测试**"不同，测试的名称不会显示，并且您将无法与它进行交互。 结果是可视化的，但您将获得摘要视图，而不是通过门户创建可用性测试时获得的相同详细视图。

要查看端到端事务详细信息，请选择"钻入下**成功**"或 **"失败"，** 然后选择示例。 您还可以通过选择图形上的数据点来获取端到端事务详细信息。

>[!div class="mx-imgBorder"]
>![选择示例可用性测试](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![端到端事务详细信息](media/availability-azure-functions/end-to-end.png)

如果运行的所有内容（不添加业务逻辑），则您将看到测试失败。

## <a name="query-in-logs-analytics"></a>日志中的查询（分析）

您可以使用 Logs（分析）来查看可用性结果、依赖项等。 要了解有关日志的详细信息，请访问[日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

>[!div class="mx-imgBorder"]
>![可用性结果](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![依赖项](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>后续步骤

- [应用程序地图](../../azure-monitor/app/app-map.md)
- [事务诊断](../../azure-monitor/app/transaction-diagnostics.md)
