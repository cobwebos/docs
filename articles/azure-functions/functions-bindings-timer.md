---
title: Azure Functions 的计时器触发器
description: 了解如何在 Azure Functions 中使用计时器触发器。
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: ebe7916900ded20dc6555c9c28277dea889539a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230970"
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions 的计时器触发器 

本文介绍如何在 Azure Functions 中使用计时器触发器。 计时器触发器可以按计划运行函数。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 2.x

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 包 2.x 版中提供了计时器触发器。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>包 - Functions 2.x

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 包 3.x 版中提供了计时器触发器。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>示例

参阅语言特定的示例：

* [C#](#c-example)
* [C# 脚本 (.csx)](#c-script-example)
* [F#](#f-example)
* [Java](#java-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>C# 示例

The following example shows a [C# function](functions-dotnet-class-library.md) that is executed each time the minutes have a value divisible by five (eg if the function starts at 18:57:00, the next performance will be at 19:00:00). The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>C# 脚本示例

以下示例演示 *function.json* 文件中的一个计时器触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数将写入日志信息，指示调用此函数是由于错过了计划发生时间。 The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

下面是 function.json 文件中的绑定数据：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# 脚本代码如下所示：

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>F# 示例

以下示例演示了 *function.json* 文件中的一个计时器触发器绑定以及使用该绑定的 [F# 脚本函数](functions-reference-fsharp.md)。 该函数将写入日志信息，指示调用此函数是由于错过了计划发生时间。 The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

下面是 function.json 文件中的绑定数据：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

F# 脚本代码如下所示：

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="java-example"></a>Java 示例

以下示例函数的触发和执行间隔为 5 分钟。 函数上的 `@TimerTrigger` 注释使用与 [CRON 表达式](https://en.wikipedia.org/wiki/Cron#CRON_expression)相同的字符串格式定义计划。

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

### <a name="javascript-example"></a>JavaScript 示例

以下示例演示 *function.json* 文件中的一个计时器触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数将写入日志信息，指示调用此函数是由于错过了计划发生时间。 A [timer object](#usage) is passed into the function.

下面是 function.json 文件中的绑定数据：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript 代码如下所示：

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

### <a name="python-example"></a>Python 示例

The following example uses a timer trigger binding whose configuration is described in the *function.json* file. The actual [Python function](functions-reference-python.md) that uses the binding is described in the *__init__.py* file. The object passed into the function is of type [azure.functions.TimerRequest object](/python/api/azure-functions/azure.functions.timerrequest). The function logic writes to the logs indicating whether the current invocation is due to a missed schedule occurrence. 

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

下面是 Python 代码：

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

## <a name="attributes"></a>属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)。

该特性的构造函数采用 CRON 表达式或 `TimeSpan`： 仅当函数应用在应用服务计划中运行时才能使用 `TimeSpan`。 以下示例显示了一个 CRON 表达式：

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `TimerTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |描述|
|---------|---------|----------------------|
|类型 | 不适用 | 必须设置为“timerTrigger”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|direction | 不适用 | 必须设置为“in”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|name | 不适用 | 在函数代码中表示计时器对象的变量的名称。 | 
|**schedule**|**ScheduleExpression**|[CRON 表达式](#ncrontab-expressions)或 [TimeSpan](#timespan) 值。 只能对在应用服务计划中运行的函数应用使用 `TimeSpan`。 可以将计划表达式放在应用设置中并将此属性设置为用 **%** 符号括起的应用设置名称，例如此示例中的“%ScheduleAppSetting%”。 |
|**runOnStartup**|**RunOnStartup**|如果为 `true`，则在运行时启动时调用此函数。 例如，当函数应用从由于无活动而进入的空闲状态醒来后，运行时会启动。 when the function app restarts due to function changes, and when the function app scales out. So **runOnStartup** should rarely if ever be set to `true`, especially in production. |
|**useMonitor**|**UseMonitor**|设置为 `true` 或 `false` 以指示是否应当监视计划。 计划监视在各次计划发生后会持续存在，以帮助确保即使在函数应用实例重新启动的情况下也能正确维护计划。 If not set explicitly, the default is `true` for schedules that have a recurrence interval greater than or equal to 1 minute. 对于每分钟触发多次的计划，默认值为 `false`。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> 在生产中不建议将 runOnStartup 设置为 `true`。 使用此设置会使代码在非常不可预测的时间执行。 在某些生产设置中，这些额外执行可能会导致消耗计划中托管的应用产生明显更高的成本。 For example, with **runOnStartup** enabled the trigger is invoked whenever your function app is scaled. 在生产中启用 runOnStartup 之前，请确保完全了解函数的生产行为。   

## <a name="usage"></a>使用情况

When a timer trigger function is invoked, a timer object is passed into the function. 以下 JSON 是计时器对象的示例表示形式。

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

如果当前函数调用晚于计划时间，则 `IsPastDue` 属性为 `true`。 例如，函数应用重新启动可能会导致调用被错过。

## <a name="ncrontab-expressions"></a>NCRONTAB expressions 

Azure Functions uses the [NCronTab](https://github.com/atifaziz/NCrontab) library to interpret NCRONTAB expressions. An NCRONTAB exppression is similar to a CRON expression except that it includes an additional sixth field at the beginning to use for time precision in seconds:

`{second} {minute} {hour} {day} {month} {day-of-week}`

每个字段可以具有下列类型之一的值：

|Type  |示例  |何时触发  |
|---------|---------|---------|
|一个具体值 |<nobr>"0 5 * * * *"</nobr>|在 hh:05:00，其中 hh 表示每小时（每小时一次）|
|所有值 (`*`)|<nobr>"0 * 5 * * *"</nobr>|在每天的 5:mm:00，其中 mm 表示该小时的每分钟（一天 60 次）|
|一个范围（`-` 运算符）|<nobr>"5-7 * * * * *"</nobr>|在 hh:mm:05、hh:mm:06 和 hh:mm:07，其中 hh:mm 表示每小时的每分钟（每分钟 3 次）|
|一组值（`,` 运算符）|<nobr>"5,8,10 * * * * *"</nobr>|在 hh:mm:05、hh:mm:08 和 hh:mm:10，其中 hh:mm 表示每小时的每分钟（每分钟 3 次）|
|一个间隔值（`/` 运算符）|<nobr>"0 */5 * * * *"</nobr>|在 hh:05:00、hh:10:00、hh:15:00，依此类推，直到 hh:55:00，其中 hh 表示每小时（每小时 12 次）|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB examples

Here are some examples of NCRONTAB expressions you can use for the timer trigger in Azure Functions.

|示例|何时触发  |
|---------|---------|
|`"0 */5 * * * *"`|每五分钟一次|
|`"0 0 * * * *"`|每小时一次（在每小时的开头）|
|`"0 0 */2 * * *"`|每两小时一次|
|`"0 0 9-17 * * *"`|从上午 9 点到下午 5 点每小时一次|
|`"0 30 9 * * *"`|每天上午 9:30|
|`"0 30 9 * * 1-5"`|每个工作日的上午 9:30|
|`"0 30 9 * Jan Mon"`|在一月份每星期一的上午 9:30|


### <a name="ncrontab-time-zones"></a>NCRONTAB time zones

CRON 表达式中的数字指的是时间和日期，而不是时间跨度。 例如，`hour` 字段中的 5 指的是 5:00 AM，而不是每 5 小时。

CRON 表达式使用的默认时区为协调世界时 (UTC)。 若要让 CRON 表达式基于其他时区，请为你的函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置。 将值设置为所需时区的名称，如 [Microsoft 时区索引](https://technet.microsoft.com/library/cc749073)中所示。 

例如，东部标准时间是 UTC-05:00。 To have your timer trigger fire at 10:00 AM EST every day, use the following NCRONTAB expression that accounts for UTC time zone:

```
"0 0 15 * * *"
``` 

或者为你的函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置并将值设置为 **Eastern Standard Time**。  Then uses the following NCRONTAB expression: 

```
"0 0 10 * * *"
``` 

当使用 `WEBSITE_TIME_ZONE`，时间将针对特定时区中的时间更改进行调整，例如夏令时。 

## <a name="timespan"></a>TimeSpan

 只能对在应用服务计划中运行的函数应用使用 `TimeSpan`。

与 CRON 表达式不同，`TimeSpan` 值指定各次函数调用之间的时间间隔。 如果函数的运行时间超出了指定的时间间隔，则在函数完成时，计时器会立即再次调用该函数。

以字符串表示，当 `hh` 小于 24 时，`TimeSpan` 格式为 `hh:mm:ss`。 当前两个数字是 24 或更大的数字时，格式为 `dd:hh:mm`。 下面是一些示例：

|示例 |何时触发  |
|---------|---------|
|"01:00:00" | 每小时        |
|"00:01:00"|每分钟         |
|"24:00:00" | every 24 hours        |
|"1.00:00:00" | every day        |

## <a name="scale-out"></a>横向扩展

如果函数应用横向扩展到多个实例，则在所有实例中只会运行由计时器触发的函数的单个实例。

## <a name="function-apps-sharing-storage"></a>共享同一存储的函数应用

If you are sharing storage accounts across function apps that are not deployed to app service, you might need to explicitly assign host ID to each app.

| Functions 版本 | 设置                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x               | `AzureFunctionsWebHost__hostid` environment variable |
| 1.x               | `id` in *host.json*                                  |

You can omit the identifying value or manually set each function app's identifying configuration to a different value.

The timer trigger uses a storage lock to ensure that there is only one timer instance when a function app scales out to multiple instances. If two function apps share the same identifying configuration and each uses a timer trigger, only one timer runs.

## <a name="retry-behavior"></a>重试行为

与队列触发器不同，计时器触发器在函数失败后不会重试。 函数失败时，在计划的下次时间到来前，不会再次调用该函数。

## <a name="troubleshooting"></a>故障排除

有关在计时器触发器未按预期工作的情况下应采取的措施，请参阅[调查和报告有关计时器触发功能未触发的问题](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到有关使用计时器触发器的快速入门](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
