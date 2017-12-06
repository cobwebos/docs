---
title: "Azure Functions 的计时器触发器"
description: "了解如何在 Azure Functions 中使用计时器触发器。"
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: 
ms.openlocfilehash: fd9c1d40ba1398c7ca3f48f0423457482da9a483
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions 的计时器触发器 

本文介绍如何在 Azure Functions 中使用计时器触发器。 计时器触发器可以按计划运行函数。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>示例

参阅语言特定的示例：

* [预编译 C#](#trigger---c-example)
* [C# 脚本](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C# 示例

以下示例演示每五分钟运行一次的[预编译 C# 函数](functions-dotnet-class-library.md)：

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
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

以下示例演示 *function.json* 文件中的一个计时器触发器绑定以及使用该绑定的 [F# 脚本函数](functions-reference-fsharp.md)。 该函数将写入日志信息，指示调用此函数是由于错过了计划发生时间。

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

对于[预编译 C#](functions-dotnet-class-library.md) 函数，请使用 NuGet 包 [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) 中定义的 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)。

该特性的构造函数采用 CRON 表达式，如以下示例中所示：

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

如果按应用服务计划（而不是消耗计划）运行函数应用，可以指定 `TimeSpan`（而不是 CRON 表达式）。

有关完整示例，请参阅[预编译 C# 示例](#c-example)。

## <a name="configuration"></a>配置

下表解释了在 *function.json* 文件和 `TimerTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为“timerTrigger”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为“in”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 在函数代码中表示计时器对象的变量的名称。 | 
|**schedule**|**ScheduleExpression**|在消耗计划中，可使用 CRON 表达式定义计划。 如果使用应用服务计划，还可使用 `TimeSpan` 字符串。 以下部分介绍 CRON 表达式。 可以将计划表达式放在应用设置中并将此属性设置为 **%** 签名中包装的值，如此示例：“%NameOfAppSettingWithCRONExpression%”中所示。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>CRON 格式 

Azure Functions 计时器触发器的 [CRON 表达式](http://en.wikipedia.org/wiki/Cron#CRON_expression)包含以下六个字段： 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>联机找到的许多 CRON 表达式会忽略 `{second}` 字段。 如果从这些字段之一复制，请添加缺少的 `{second}` 字段。

### <a name="cron-time-zones"></a>CRON 时区

CRON 表达式使用的默认时区为协调世界时 (UTC)。 若要让 CRON 表达式基于其他时区，请为名为 `WEBSITE_TIME_ZONE` 的 Function App 创建新的应用设置。 将值设置为所需时区的名称，如 [Microsoft 时区索引](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx)中所示。 

例如，东部标准时间是 UTC-05:00。 若要让计时器触发器每天在美国东部时间上午 10:00 触发，可使用表示 UTC 时区的以下 CRON 表达式：

```json
"schedule": "0 0 15 * * *",
``` 

或者，可以为名为 `WEBSITE_TIME_ZONE` 的 Function App 添加新的应用设置，并将值设置为**东部标准时间**。  然后，以下 CRON 表达式可用于东部时间上午 10:00： 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>CRON 示例

以下是一些可用于 Azure Functions 中计时器触发器的 CRON 表达式示例。 

每隔五分钟触发一次：

```json
"schedule": "0 */5 * * * *"
```

每隔一小时开始时触发一次：

```json
"schedule": "0 0 * * * *",
```

每隔两小时触发一次：

```json
"schedule": "0 0 */2 * * *",
```

从上午 9 点到下午 5 点每隔一小时触发一次：

```json
"schedule": "0 0 9-17 * * *",
```

每天上午 9:30 触发：

```json
"schedule": "0 30 9 * * *",
```

每周日上午 9:30 触发：

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>使用情况

调用计时器触发器函数时，[计时器对象](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)传递到函数中。 以下 JSON 是计时器对象的示例表示形式。 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>横向扩展

计时器触发器支持多实例扩展。特定计时器函数的单个实例在所有实例上运行。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到有关使用计时器触发器的快速入门](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
