---
title: "Azure Functions 计时器触发器 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用计时器触发器。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
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
ms.author: chrande; glenga
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 12a793c4df497f221dbd592ca3d249b8c1f65e04
ms.contentlocale: zh-cn
ms.lasthandoff: 06/22/2017


---
# <a name="azure-functions-timer-trigger"></a>Azure Functions 计时器触发器

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍了如何在 Azure Functions 中对计时器触发器进行配置和编写代码。 Azure Functions 有一个计时器触发器绑定，允许用户根据定义的计划运行函数代码。 

计时器触发器支持多实例扩展。 特定计时器函数的单个实例在所有实例上运行。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>计时器触发器
函数的计时器触发器使用下列 JSON 对象（这些对象位于 function.json 的 `bindings` 数组中）：

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

`schedule` 的值是 [CRON 表达式](http://en.wikipedia.org/wiki/Cron#CRON_expression)，其中包含以下六个字段： 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>许多在线的 cron 表达式会忽略 `{second}` 字段。 如果从其中一个进行复制，需要调整额外的 `{second}` 字段。 有关具体示例，请参阅下面的[计划示例](#examples)。

CRON 表达式使用的默认时区为协调世界时 (UTC)。 若要让 CRON 表达式基于其他时区，请为名为 `WEBSITE_TIME_ZONE` 的 Function App 创建新的应用设置。 将值设置为所需时区的名称，如 [Microsoft 时区索引](https://msdn.microsoft.com/library/ms912391.aspx)中所示。 

例如，东部标准时间是 UTC-05:00。 若要让计时器触发器每天在美国东部时间上午 10:00 触发，可使用表示 UTC 时区的以下 CRON 表达式：

```json
"schedule": "0 0 15 * * *",
``` 

或者，可以为名为 `WEBSITE_TIME_ZONE` 的 Function App 添加新的应用设置，并将值设置为**东部标准时间**。  然后，以下 CRON 表达式可用于东部时间上午 10:00： 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>计划示例
以下是可用于 `schedule` 属性的 CRON 表达式的一些示例。 

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

<a name="usage"></a>

## <a name="trigger-usage"></a>触发器使用情况
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

<a name="sample"></a>

## <a name="trigger-sample"></a>触发器示例
假设在 function.json 的 `bindings` 数组中有以下计时器触发器：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

请参阅读取计时器对象的特定于语言的示例，以查看是否延迟运行。

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C# 中的触发器示例 #
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

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F# 中的触发器示例 #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的触发器示例
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

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


