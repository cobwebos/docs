---
title: Durable Functions 中的监视器 - Azure
description: 了解如何使用 Azure Functions 的 Durable Functions 扩展实现状态监视。
services: functions
author: kashimiz
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 9cb7a076ea922b9868bd439d160aec96f044e3b6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Durable Functions 中的监视场景 - 天气观察程序示例

监视模式是工作流中灵活的重复过程 - 例如，反复轮询，直到满足特定的条件为止。 本文介绍使用 [Durable Functions](durable-functions-overview.md) 实现监视的示例。

## <a name="prerequisites"></a>先决条件

* [安装 Durable Functions](durable-functions-install.md)。
* 完成 [Hello 序列](durable-functions-sequence.md)演练。

## <a name="scenario-overview"></a>方案概述

此示例监视某个地点的当前天气状况，如果是晴天，则通过短信通知用户。 可以使用常规的计时器触发函数来检查天气和发送提醒。 但是，此方法存在**生存期管理**方面的问题。 如果只应发送一条提醒，则在检测到晴天后，监视器需要禁用自身。 监视模式可以结束自身的执行，同时还具有其他优点：

* 监视器按时间间隔而不是计划运行：计时器触发器每隔一小时运行；监视器等待一小时，然后执行下一项操作。 除非有指定，否则监视器的操作不会重叠，这对于长时间运行的任务可能很重要。
* 监视器可以使用动态时间间隔：可以根据某种条件更改等待时间。
* 监视器可以在满足某种条件时终止，或者由其他进程终止。
* 监视器可以采用参数。 此示例演示如何将同一个天气监视进程应用到任何请求的地点和电话号码。
* 监视器可缩放。 由于每个监视器是一个业务流程实例，因此可以创建多个监视器，而无需创建新函数或定义更多的代码。
* 监视器可轻松集成到更大的工作流。 监视器可以是更复杂业务流程函数或[子业务流程](https://docs.microsoft.com/azure/azure-functions/durable-functions-sub-orchestrations)的一部分。

## <a name="configuring-twilio-integration"></a>配置 Twilio 集成

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>配置 Weather Underground 集成

此示例涉及到使用 Weather Underground API 来检查某个地点的当前天气状况。

首先需要创建一个 Weather Underground 帐户。 可以通过 [https://www.wunderground.com/signup](https://www.wunderground.com/signup) 免费创建一个帐户。 创建帐户后，需要获取 API 密钥。 可以访问 [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api)，然后选择“密钥设置”来获取此密钥。 Stratus Developer 计划是免费的，足以用于运行此示例。

获取 API 密钥后，将以下**应用设置**添加到函数应用。

| 应用设置名称 | 值说明 |
| - | - |
| **WeatherUndergroundApiKey**  | Weather Underground API 密钥。 |

## <a name="the-functions"></a>函数

本文介绍示例应用中的以下函数：

* `E3_Monitor`：定期调用 `E3_GetIsClear` 的业务流程协调程序函数。 如果 `E3_GetIsClear` 返回 true，则此函数会调用 `E3_SendGoodWeatherAlert`。
* `E3_GetIsClear`：检查某个地点的当前天气状况的活动函数。
* `E3_SendGoodWeatherAlert`：通过 Twilio 发送短信的活动函数。

以下部分介绍用于 C# 脚本的配置和代码。 文章末尾展示了用于 Visual Studio 开发的代码。
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>天气监视业务流程（Visual Studio Code 和 Azure 门户示例代码）

**E3_Monitor** 函数对业务流程协调程序函数使用标准的 *function.json*。

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

实现该函数的代码如下：

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

此业务流程协调程序函数执行以下操作：

1. 获取 **MonitorRequest**，其中包括要监视的地点，以及要将短信通知发送到的电话号码。
2. 确定监视器的过期时间。 为简便起见，本示例使用了硬编码值。
3. 调用 **E3_GetIsClear** 来确定请求的地点是否为晴天。
4. 如果是晴天，则调用 **E3_SendGoodWeatherAlert** 将短信通知发送到请求的电话号码。
5. 创建一个持久计时器，以便在下一个轮询间隔恢复业务流程。 为简便起见，本示例使用了硬编码值。
6. 持续运行，直到 [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) 超过了监视器的过期时间或者发送了短信提醒。

可以通过发送多个 **MonitorRequests** 来同时运行多个业务流程实例。 可以指定要监视的地点，以及要将短信提醒发送到的电话号码。

## <a name="strongly-typed-data-transfer"></a>强类型数据传输

业务流程协调程序需要多个数据片断，因此，对强类型数据传输使用了[共享的 POCO 对象](functions-reference-csharp.md#reusing-csx-code)：[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

## <a name="helper-activity-functions"></a>帮助器活动函数

与其他示例一样，帮助器活动函数是使用 `activityTrigger` 触发器绑定的正则函数。 **E3_GetIsClear** 函数使用 Weather Underground API 获取当前天气状况并确定是否为晴天。 function.json 定义如下：

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

实现如下。 与用于数据传输的 POCO 一样，用于处理 API 调用和分析响应 JSON 的逻辑已抽象化为共享类。 可以在 [Visual Studio 示例代码](#run-the-sample)中找到此逻辑。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

**E3_SendGoodWeatherAlert** 函数使用 Twilio 绑定来发送短信，告知最终用户目前的天气适合散步。 该函数的 *function.json* 十分简单：

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

下面是发送短信的代码：

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

## <a name="run-the-sample"></a>运行示例

使用示例中包含的 HTTP 触发型函数，可以通过发送以下 HTTP POST 请求来启动业务流程：

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "Location": { "City": "Redmond", "State": "WA" }, "Phone": "+1425XXXXXXX" }
```
```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** 实例启动，并查询请求位置的当前天气状况。 如果天气为晴，则调用某个活动函数来发送提醒；否则将设置计时器。 当计时器过期时，业务流程将会恢复。

可以通过查看 Azure Functions 门户中的函数日志，来查看业务流程的活动。

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

业务流程的超时时间已到，或者检测到晴天时，业务流程将会[终止](durable-functions-instance-management.md#terminating-instances)。 也可以在另一函数中使用 `TerminateAsync`，或调用上述 202 响应中提到的 **terminatePostUri** HTTP POST Webhook（请将 `{text}` 替换为终止原因）：

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio 示例代码

下面是 Visual Studio 项目中以单个 C# 文件形式提供的业务流程：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>后续步骤

本示例演示了如何使用 Durable Functions，通过[持久性计时器](durable-functions-timers.md)和条件逻辑来监视外部源的状态。 下一个示例演示如何使用外部事件和[持久计时器](durable-functions-timers.md)处理人工交互。

> [!div class="nextstepaction"]
> [运行人工交互示例](durable-functions-phone-verification.md)
