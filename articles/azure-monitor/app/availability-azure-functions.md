---
title: 使用 Azure Functions 创建和运行自定义可用性测试
description: 本文档将介绍如何创建包含 TrackAvailability （）的 Azure 函数，该函数将根据 TimerTrigger 函数中给定的配置定期运行。 此测试的结果将发送到 Application Insights 资源，你可以在其中查询可用性结果数据并对其发出警报。 自定义测试使你可以编写比使用门户 UI 更复杂的可用性测试、监视 Azure VNET 内的应用、更改终结点地址，或者在你的区域中不可用时创建可用性测试。
services: application-insights
documentationcenter: ''
author: morgangrobin
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: mogrobin
ms.openlocfilehash: 38a83169a7d1ffa03416f5947ada703bcba5017a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301368"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>使用 Azure Functions 创建和运行自定义可用性测试

本文介绍如何使用 TrackAvailability （）创建 Azure 函数，该函数将根据 TimerTrigger 函数中给定的配置定期运行。 此测试的结果将发送到 Application Insights 资源，你可以在其中查询可用性结果数据并对其发出警报。 这允许您创建类似于通过门户中的[可用性监视](../../azure-monitor/app/monitor-web-app-availability.md)来实现的自定义测试。 自定义的测试允许编写比使用门户 UI 更复杂的可用性测试、监视 Azure VNET 内的应用、更改终结点地址，或者即使此功能在你的区域中不可用，也可以创建可用性测试。


## <a name="create-timer-triggered-function"></a>创建计时器触发的函数

- 如果有 Application Insights 资源：
    - 默认情况下 Azure Functions 创建 Application Insights 资源，但如果想要使用已创建的某个资源，则需在创建过程中指定。
    - 按照以下选项操作，了解如何[创建 Azure Functions 资源和计时器触发的函数](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)（在清理之前停止）。
        -  选择 "**创建**" 之前，单击 "Application Insights" 部分。

            ![ 使用自己的 App Insights 资源创建 Azure Functions 应用](media/availability-azure-functions/create-function-app.png)

        - 单击 "**选择现有资源**" 并键入资源的名称。 选择**应用**

            ![选择现有 Application Insights 资源](media/availability-azure-functions/app-insights-resource.png)

        - 选择“创建”
- 如果尚未为计时器触发的函数创建 Application Insights 资源：
    - 默认情况下，在创建 Azure Functions 应用程序时，它将为你创建一个 Application Insights 资源。
    - 按照有关如何[创建 Azure Functions 资源和计时器触发函数](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)（在清理之前停止）的说明进行操作。

## <a name="sample-code"></a>示例代码

将下面的代码复制到 run.csx 文件中（这将替换预先存在的代码）。 为此，请进入 Azure Functions 应用程序，并选择左侧的计时器触发器函数。

![Azure 门户中的 Azure 函数的 run.csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> 对于要使用的终结点地址： `EndpointAddress= https://dc.services.visualstudio.com/v2/track`。 除非你的资源位于 Azure 政府版或 Azure 中国等区域，否则请参阅此文，了解如何[覆盖默认终结点](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification)，并选择适用于你所在区域的相应遥测通道终结点。

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
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
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![在右侧选择 "添加"。 将文件命名为 proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>查看可用性

若要确保一切正常，可以在 Application Insights 资源的 "可用性" 选项卡中查看图形。

![成功结果的 "可用性" 选项卡](media/availability-azure-functions/availtab.png)

Azure Functions 使用在设置测试时，你会注意到，与在 "可用性" 选项卡中使用 "**添加测试**" 不同，你的测试的名称将不会出现，并且你将无法与之进行交互。 将显示结果，但会获得一个摘要视图，而不是通过门户创建可用性测试时获得的相同详细视图。

若要查看端到端事务详细信息，请在 "深化到" 下选择 "**成功**" 或 "**失败**"，然后选择一个示例。 还可以通过选择关系图上的数据点来访问端到端事务详细信息。

![选择示例可用性测试](media/availability-azure-functions/sample.png)

![端到端事务详细信息](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>在日志中查询（Analytics）

你可以使用日志（分析）来查看可用性结果、依赖关系等。 若要了解有关日志的详细信息，请访问[日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

![可用性结果](media/availability-azure-functions/availabilityresults.png)

![依赖项](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>后续步骤

- [应用程序映射](../../azure-monitor/app/app-map.md)
- [事务诊断](../../azure-monitor/app/transaction-diagnostics.md)
