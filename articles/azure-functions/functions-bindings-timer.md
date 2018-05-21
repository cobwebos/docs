---
title: Azure Functions 的计时器触发器
description: 了解如何在 Azure Functions 中使用计时器触发器。
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions，函数，事件处理，动态计算，无服务体系结构
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: a8844ea44bf604944c5980b0d41ab5d01a30b876
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions 的计时器触发器 

本文介绍如何在 Azure Functions 中使用计时器触发器。 计时器触发器可以按计划运行函数。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>包

[Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 包中提供了计时器触发器。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="example"></a>示例

参阅语言特定的示例：

* [C#](#trigger---c-example)
* [C# 脚本 (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C# 示例

以下示例演示每五分钟运行一次的 [C# 函数](functions-dotnet-class-library.md)：

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>C# 脚本示例

以下示例演示 *function.json* 文件中的一个计时器触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数将写入日志信息，指示调用此函数是由于错过了计划发生时间。

下面是 *function.json* 文件中的绑定数据：

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
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>F# 示例

以下示例演示了 *function.json* 文件中的一个计时器触发器绑定以及使用该绑定的 [F# 脚本函数](functions-reference-fsharp.md)。 该函数将写入日志信息，指示调用此函数是由于错过了计划发生时间。

下面是 *function.json* 文件中的绑定数据：

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
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript 示例

以下示例演示 *function.json* 文件中的一个计时器触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数将写入日志信息，指示调用此函数是由于错过了计划发生时间。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript 脚本代码如下所示：

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)。

该特性的构造函数采用 CRON 表达式或 `TimeSpan`： 仅当函数应用在应用服务计划中运行时才能使用 `TimeSpan`。 以下示例显示了一个 CRON 表达式：

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>配置

下表解释了在 *function.json* 文件和 `TimerTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为“timerTrigger”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为“in”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 在函数代码中表示计时器对象的变量的名称。 | 
|**schedule**|**ScheduleExpression**|[CRON 表达式](#cron-expressions)或 [TimeSpan](#timespan) 值。 只能对在应用服务计划中运行的函数应用使用 `TimeSpan`。 可以将计划表达式放在应用设置中并将此属性设置为用 **%** 符号括起的应用设置名称，例如此示例中的“%ScheduleAppSetting%”。 |
|**runOnStartup**|**RunOnStartup**|如果为 `true`，则在运行时启动时调用此函数。 例如，当函数应用从由于无活动而进入的空闲状态醒来后，运行时会启动。 当函数应用由于函数更改而重新启动时，以及当函数应用横向扩展时。因此，**runOnStartup** 在极少的情况下才应该设置为 `true`，因为这会使代码的执行时间很难预测。|
|**useMonitor**|**UseMonitor**|设置为 `true` 或 `false` 以指示是否应当监视计划。 计划监视在各次计划发生后会持续存在，以帮助确保即使在函数应用实例重新启动的情况下也能正确维护计划。 如果未显式设置，则对于重复周期间隔大于 1 分钟的计划，默认值为 `true`。 对于每分钟触发多次的计划，默认值为 `false`。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

调用计时器触发器函数时，[计时器对象](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)传递到函数中。 以下 JSON 是计时器对象的示例表示形式。 

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

## <a name="cron-expressions"></a>CRON 表达式 

Azure Functions 计时器触发器的 CRON 表达式包含以下六个字段： 

`{second} {minute} {hour} {day} {month} {day-of-week}`

每个字段可以具有下列类型之一的值：

|Type  |示例  |何时触发  |
|---------|---------|---------|
|一个具体值 |<nobr>"0 5 * * * *"</nobr>|在 hh:05:00，其中 hh 表示每小时（每小时一次）|
|所有值 (`*`)|<nobr>"0 * 5 * * *"</nobr>|在每天的 5:mm:00，其中 mm 表示该小时的每分钟（一天 60 次）|
|一个范围（`-` 运算符）|<nobr>"5-7 * * * * *"</nobr>|在 hh:mm:05、hh:mm:06 和 hh:mm:07，其中 hh:mm 表示每小时的每分钟（每分钟 3 次）|  
|一组值（`,` 运算符）|<nobr>"5,8,10 * * * * *"</nobr>|在 hh:mm:05、hh:mm:08 和 hh:mm:10，其中 hh:mm 表示每小时的每分钟（每分钟 3 次）|
|一个间隔值（`/` 运算符）|<nobr>"0 */5 * * * *"</nobr>|在 hh:05:00、hh:10:00、hh:15:00，依此类推，直到 hh:55:00，其中 hh 表示每小时（每小时 12 次）|

### <a name="cron-examples"></a>CRON 示例

以下是一些可用于 Azure Functions 中计时器触发器的 CRON 表达式示例。

|示例|何时触发  |
|---------|---------|
|"0 */5 * * * *"|每五分钟一次|
|"0 0 * * * *"|每小时一次（在每小时的开头）|
|"0 0 */2 * * *"|每两小时一次|
|"0 0 9-17 * * *"|从上午 9 点到下午 5 点每小时一次|
|"0 30 9 * * *"|每天上午 9:30|
|"0 30 9 * * 1-5"|每个工作日的上午 9:30|

>[!NOTE]   
>你可以在线找到 CRON 表达式示例，但它们中的许多都省略了 `{second}` 字段。 如果从这些字段之一复制，请添加缺少的 `{second}` 字段。 通常，你希望该字段的值为零，而不是星号。

### <a name="cron-time-zones"></a>CRON 时区

CRON 表达式中的数字指的是时间和日期，而不是时间跨度。 例如，`hour` 字段中的 5 指的是 5:00 AM，而不是每 5 小时。

CRON 表达式使用的默认时区为协调世界时 (UTC)。 若要让 CRON 表达式基于其他时区，请为你的函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置。 将值设置为所需时区的名称，如 [Microsoft 时区索引](https://technet.microsoft.com/library/cc749073)中所示。 

例如，东部标准时间是 UTC-05:00。 若要让计时器触发器每天在美国东部时间上午 10:00 触发，可使用表示 UTC 时区的以下 CRON 表达式：

```json
"schedule": "0 0 15 * * *",
``` 

或者为你的函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置并将值设置为 **Eastern Standard Time**。  然后使用以下 CRON 表达式： 

```json
"schedule": "0 0 10 * * *",
``` 

## <a name="timespan"></a>TimeSpan

 只能对在应用服务计划中运行的函数应用使用 `TimeSpan`。

与 CRON 表达式不同，`TimeSpan` 值指定各次函数调用之间的时间间隔。 如果函数的运行时间超出了指定的时间间隔，则在函数完成时，计时器会立即再次调用该函数。

以字符串表示，当 `hh` 小于 24 时，`TimeSpan` 格式为 `hh:mm:ss`。 当前两个数字是 24 或更大的数字时，格式为 `dd:hh:mm`。 下面是一些示例：

|示例 |何时触发  |
|---------|---------|
|"01:00:00" | 每小时        |
|"00:01:00"|每分钟         |
|"24:00:00" | 每 24 天        |

## <a name="scale-out"></a>横向扩展

如果函数应用横向扩展到多个实例，则在所有实例中只会运行由计时器触发的函数的单个实例。

## <a name="function-apps-sharing-storage"></a>共享同一存储的函数应用

如果在多个函数应用之间共享存储帐户，请确保每个函数应用在 *host.json* 中都有不同的 `id`。 可以省略 `id` 属性或手动将每个函数应用的 `id` 设置为不同的值。 计时器触发器使用存储锁来确保当函数应用横向扩展到多个实例时将只有一个计时器实例。 如果两个函数应用共享相同的 `id` 且每个都使用计时器触发器，则只会运行一个计时器。

## <a name="retry-behavior"></a>重试行为

与队列触发器不同，计时器触发器在函数失败后不会重试。 当函数失败时，会再次调用该函数，直到计划的下次时间。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到有关使用计时器触发器的快速入门](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
