---
title: 概念、术语和实体 - Azure 计划程序 | Microsoft Docs
description: 了解 Azure 计划程序的概念、术语和实体层次结构，包括作业和作业集合
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.topic: conceptual
ms.date: 08/18/2016
ms.openlocfilehash: d701fba39685d781d1a4c2d8a6cf194ca7eb2908
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683046"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Azure 计划程序的概念、术语和实体

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)正在替换即将停用的 Azure 计划程序。 若要计划作业，请[改用 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 

## <a name="entity-hierarchy"></a>实体层次结构

Azure 计划程序 REST API 公开并使用以下主要实体或资源：

| 实体 | 描述 |
|--------|-------------|
| **作业** | 通过用于执行的简单或复杂策略定义单个定期操作。 操作可以包括 HTTP、存储队列、服务总线队列或服务总线主题请求。 | 
| **作业集合** | 包含一组作业，并且维护集合内各作业共享的设置、配额和限制。 作为 Azure 订阅所有者，你可以创建作业集合，并根据使用情况或应用程序边界将作业分组。 作业集合具有以下特性： <p>- 限制在一个区域。 <br>- 可以强制执行配额，以便限制集合中所有作业的使用。 <br>- 配额包括 MaxJobs 和 MaxRecurrence. | 
| **作业历史记录** | 描述作业执行的详细信息，例如状态和任何响应详细信息。 |
||| 

## <a name="entity-management"></a>实体管理

计划程序 REST API 在高级别公开这些操作以管理实体。

### <a name="job-management"></a>作业管理

支持创建和编辑作业的操作。 所有作业都必须属于某一现有作业集合，因此没有显式创建。 有关详细信息，请参阅[计划程序 REST API - 作业](https://docs.microsoft.com/rest/api/scheduler/jobs)。 下面是这些操作的 URI 地址：

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}
```

### <a name="job-collection-management"></a>作业集合管理

支持创建和编辑作业和作业集合（映射到配额和共享设置）的操作。 例如，配额指定最大作业数量和最小重复周期间隔。 有关详细信息，请参阅[计划程序 REST API - 作业集合](https://docs.microsoft.com/rest/api/scheduler/jobcollections)。 下面是这些操作的 URI 地址：

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}
```

### <a name="job-history-management"></a>作业历史记录管理

支持用于获取 60 天的作业执行历史记录（例如，作业已用时间和作业执行结果）的 GET 操作。 包含基于状态进行筛选的查询字符串参数支持。 有关详细信息，请参阅[计划程序 REST API - 作业 - 列出作业历史记录](https://docs.microsoft.com/rest/api/scheduler/jobs/listjobhistory)。 下面是此操作的 URI 地址：

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history
```

## <a name="job-types"></a>作业类型

Azure 计划程序支持多个作业类型： 

* 当你拥有现有服务或工作负载的终结点时的 HTTP 作业（包括支持 SSL 的 HTTPS 作业）
* 针对使用存储队列的工作负载的存储队列作业，例如将消息发布到存储队列
* 针对使用服务总线队列的工作负载的服务总线队列作业
* 针对使用服务总线主题的工作负载的服务总线主题作业

## <a name="job-definition"></a>作业定义

在高级别，计划程序作业包含以下基本部分：

* 在引发作业计时器时运行的操作
* 可选：运行作业的时间
* 可选：重复作业的时间和频率
* 可选：主操作失败时运行的错误操作

作业还包括系统提供的数据，例如，作业的下一次计划运行时间。 作业的代码定义是一个 JavaScript 对象表示法 (JSON) 格式的对象，包括以下元素：

| 元素 | 需要 | 描述 | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | 否 | 作业的开始时间，时区偏移量为 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601) | 
| [**action**](#action) | 是 | 主操作的详细信息，可以包含 errorAction 对象 | 
| [**errorAction**](#error-action) | 否 | 主操作失败时运行的辅助操作的详细信息 |
| [**recurrence**](#recurrence) | 否 | 定期作业的频率和间隔等详细信息 | 
| [**retryPolicy**](#retry-policy) | 否 | 有关重试操作的频率的详细信息 | 
| [**state**](#state) | 是 | 作业当前状态的详细信息 |
| [status](#status) | 是 | 作业当前状态的详细信息，由服务控制 |
||||

下面的示例显示了 HTTP 操作的综合作业定义，后面的部分中描述了更全面的元素详细信息： 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

在 startTime 对象中，可以指定 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)的开始时间和时区偏移量。

<a name="action"></a>

## <a name="action"></a>action

计划程序作业基于指定的计划运行主操作。 计划程序支持 HTTP、存储队列、服务总线队列和服务总线主题操作。 如果主操作失败，计划程序可以运行辅助 [**errorAction**](#erroraction) 处理该错误。 操作对象描述了以下元素：

* 操作的服务类型
* 操作的详细信息
* 替代的 errorAction

上一个示例介绍了 HTTP 操作。 下面是存储队列操作的示例：

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

下面是服务总线队列操作的示例：

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

下面是服务总线主题操作的示例：

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

有关共享访问签名 (SAS) 令牌的详细信息，请参阅[使用共享访问签名授权](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

如果作业的主操作失败，计划程序可以运行 errorAction 处理该错误。 在主操作中，可以指定 errorAction 对象，使计划程序可以调用错误处理的终结点或发送用户通知。 

例如，如果主终结点发生灾难，可以使用 errorAction 调用辅助终结点，或者通知错误处理终结点。 

与主操作类似，可以使用基于其他操作的简单或复合逻辑来执行错误操作。 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

如果作业的 JSON 定义包含 recurrence 对象，则作业定期发生，例如：

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| 属性 | 需要 | Value | 描述 | 
|----------|----------|-------|-------------| 
| **frequency** | 是，使用 recurrence 时 | Minute、Hour、Day、Week、Month、Year | 两次作业之间的时间单位 | 
| **interval** | 否 | 1 - 1000（包含） | 一个正整数，根据频率确定两次作业之间的时间单位数 | 
| **schedule** | 否 | 多种多样 | 更复杂和更高级计划的详细信息。 请参阅 hours、minutes、weekDays、months 和 monthDays | 
| **小时数** | 否 | 1 - 24 | 一个带有小时标记的数组，用于指示何时运行作业 | 
| **分钟数** | 否 | 0-59 | 一个带有分钟标记的数组，用于指示何时运行作业 | 
| **months** | 否 | 1 - 12 | 一个带有月份标记的数组，用于指示何时运行作业 | 
| **monthDays** | 否 | 多种多样 | 一个带有月份天数标记的数组，用于指示何时运行作业 | 
| **工作日** | 否 | Monday、Tuesday、Wednesday、Thursday、Friday、Saturday、Sunday | 一个带有星期的天数标记的数组，用于指示何时运行作业 | 
| **count** | 否 | <无> | 重复周期的次数。 默认为无限重复。 不能同时使用 count 和 endTime，但首先完成的规则优先。 | 
| **endTime** | 否 | <无> | 停止重复周期的日期和时间。 默认为无限重复。 不能同时使用 count 和 endTime，但首先完成的规则优先。 | 
||||

有关这些元素的详细信息，请参阅[生成复杂的计划和高级重复周期](../scheduler/scheduler-advanced-complexity.md)。

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

对于计划程序作业可能失败的情况，可以设置重试策略，确定计划程序是否重试操作以及如何重试。 默认情况下，计划程序以 30 秒的间隔继续重试作业四次。 可以使此策略或多或少具有攻击性，例如，此策略每天重试一个操作达两次：

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| 属性 | 需要 | Value | 描述 | 
|----------|----------|-------|-------------| 
| **retryType** | 是 | **Fixed** **None** | 确定是否指定重试策略（固定）或（无）。 | 
| **retryInterval** | 否 | PT30S | 指定每次重试尝试之间的间隔和频率（[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)）。 最小值为 15 秒，最大值为 18 个月。 | 
| **retryCount** | 否 | 4 | 指定重试尝试的次数。 最大值为 20。 | 
||||

有关详细信息，请参阅[高可用性和可靠性](../scheduler/scheduler-high-availability-reliability.md)。

<a name="status"></a>

## <a name="state"></a>state

作业的状态为“已启用”、“已禁用”、“已完成”或“已出错”，例如： 

`"state": "Disabled"`

若要将作业更改为“已启用”或“已禁用”状态，可以使用这些作业上的 PUT 或 PATCH 操作。
尽管可以对作业执行 DELETE 操作，但是，如果作业为“已完成”或“已出错”状态，则无法更新状态。 计划程序在 60 天后删除已完成的作业和已出错的作业。 

<a name="status"></a>

## <a name="status"></a>status

作业启动后，计划程序通过 status 对象返回作业状态的相关信息，它仅由计划程序控制。 不过，可以在 job 对象内找到 status 对象。 下面是作业的状态包含的信息：

* 上一次执行的时间（如果有）
* 正在进行的作业的下一次计划执行时间
* 作业执行次数
* 失败次数（如果有）
* 出错次数（如果有）

例如：

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="see-also"></a>另请参阅

* [什么是 Azure 计划程序？](scheduler-intro.md)
* [概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [构建复杂的计划和高级重复周期](scheduler-advanced-complexity.md)
* [限制、配额、默认值和错误代码](scheduler-limits-defaults-errors.md)
* [Azure 计划程序 REST API 参考](/rest/api/scheduler)
* [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)
