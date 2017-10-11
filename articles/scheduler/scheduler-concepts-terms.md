---
title: "计划程序的概念、 术语和实体 |Microsoft 文档"
description: "Azure 计划程序的概念、 术语和实体层次结构，包括作业和作业集合。  显示计划作业的综合示例。"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 0f035b58ccd140a5481703df7e184206da2ed651
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>计划程序的概念、 术语和实体层次结构
## <a name="scheduler-entity-hierarchy"></a>计划程序实体层次结构
下表描述公开或使用计划程序 API 的主要资源：

| 资源 | Description |
| --- | --- |
| **作业集合** |作业集合包含一组作业，并且维护设置、 配额和集合内各作业共享的中止值。 某一作业集合由订阅所有者创建，并且组合在一起基于使用情况或应用程序边界将作业。 它被约束到一个区域。 它还允许强制执行配额以便约束对该集合中的所有作业的使用。 该配额包括 MaxJobs 和 MaxRecurrence。 |
| **作业** |一个作业定义单个重复发生的操作，简单或复杂用于执行的策略。 操作可能包括 HTTP、 存储队列、 service bus 队列或服务总线主题请求。 |
| **作业历史记录** |作业历史记录表示用于执行作业的详细信息。 它包含成功与失败，以及任何响应详细信息。 |

## <a name="scheduler-entity-management"></a>计划程序实体管理
在高级别中，计划程序和服务管理 API 公开资源上的以下操作：

| 功能 | 说明和 URI 地址 |
| --- | --- |
| **作业集合管理** |GET、 PUT 和 DELETE 支持用于创建和修改作业集合和其中包含的作业。 作业集合是作业和指向配额和共享的设置的映射的容器。 配额，后面介绍的例子包括作业和最小重复间隔最大数量。 <p>PUT 和 DELETE:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>获取：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **作业管理** |GET、 PUT、 POST、 PATCH、 和删除用于创建和修改作业的支持。 所有作业必须都属于某一作业集合已存在，因此没有隐式创建。 <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **作业历史记录管理** |获取用于获取 60 天的作业执行历史记录，例如作业占用时间和作业执行结果的支持。 添加基于状态和状态进行筛选的查询字符串参数支持。 <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>作业类型
有多个类型的作业： HTTP 作业 （包括支持 SSL 的 HTTPS 作业）、 存储队列作业、 服务总线队列作业和服务总线主题作业。 HTTP 作业是理想选择，如果你有现有的工作负载或服务的终结点。 存储队列作业可用于将消息发布到存储队列，因此这些作业适合使用存储队列的工作负载。 同样，服务总线作业是使用 service bus 队列和主题的工作负荷的理想选择。

## <a name="the-job-entity-in-detail"></a>"作业"实体详述
在基本级别，计划的作业具有若干部分：

* 在作业计时器引发时要执行的操作  
* （可选）运行作业的时间  
* （可选）何时以及如何通常重复作业  
* （可选）要触发如果主操作失败的操作  

在内部，计划的作业还包含系统提供的数据，例如下一步计划的执行时间。

以下代码提供了全面的示例计划作业。 后续部分中提供了详细信息。

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

如上面的示例计划作业中所示，一个作业定义具有若干部分：

* 开始时间 ("startTime")  
* 操作 ("action")，包括错误操作 ("errorAction")
* 重复周期 ("recurrence")  
* 状态 ("state")  
* 状态 ("status")  
* 重试策略 ("retryPolicy")  

我们来讨论其中详细信息每一项：

## <a name="starttime"></a>startTime
"StartTime"是开始时间，允许调用方在在线指定时区偏移量[ISO 8601 格式](http://en.wikipedia.org/wiki/ISO_8601)。

## <a name="action-and-erroraction"></a>action 和 errorAction
"Action"是每次调用的操作，并且描述服务调用的类型。 操作是按提供的计划将执行的内容。 计划程序支持 HTTP、 存储队列、 服务总线主题和服务总线队列操作。

上面的示例中的操作是一个 HTTP 操作。 下面是存储队列操作的示例：

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

下面是服务总线主题操作的示例。

  "action": {"type":"为 serviceBusTopic"，"serviceBusTopicMessage": {"topicPath":"t1"  
      "命名空间":"mySBNamespace"，"transportType":"netMessaging"/ / 可以是 netMessaging 或 AMQP"身份验证": {"sasKeyName":"QPolicy"，"type":"sharedAccessKey"}，"消息":"某些消息"，"brokeredMessageProperties": {}，"customMessageProperties": {"appname":"FromScheduler"}}，}

下面是服务总线队列操作的示例：

  "action": {"serviceBusQueueMessage": {"queueName":"q1"  
      "命名空间":"mySBNamespace"，"transportType":"netMessaging"/ / 可以是 netMessaging 或 AMQP"身份验证": {  
        "sasKeyName":"QPolicy"，"type":"sharedAccessKey"}，"消息":"某些消息"  
      "brokeredMessageProperties": {}，"customMessageProperties": {"appname":"FromScheduler"}}，"type":"为 serviceBusQueue"}

"ErrorAction"是错误处理程序，在主操作失败时调用的操作。 你可以使用此变量调用错误处理终结点或发送用户通知。 这可以用于访问辅助终结点在主不可用 （例如，如果在终结点的站点灾难） 的情况下，或者可以用于通知错误处理终结点。 就像在主操作，错误操作可以是基于其他操作的简单或复合逻辑。 若要了解如何创建一个 SAS 令牌，请参阅[创建和使用共享访问签名](https://msdn.microsoft.com/library/azure/jj721951.aspx)。

## <a name="recurrence"></a>重复执行
重复周期具有若干部分：

* 频率： 分钟、 小时、 天、 周、 月、 年之一  
* 针对重复周期的按给定频率的间隔： 间隔  
* 规定的计划： 指定分钟、 小时、 工作日、 月和的重复周期的 monthdays  
* 计数： 重复周期的计数  
* 结束时间： 指定的结束时间后将不执行任何作业  

如果它具有重复执行的对象在其 JSON 定义中指定，则将重复执行作业。 如果指定计数和结束时间，则遵循首先发生的完成规则。

## <a name="state"></a>state
作业的状态是四个值之一： 启用、 禁用、 已完成，或出现故障。 你可以 PUT 或 PATCH 作业以便将它们更新到启用或禁用状态。 如果作业已完成或出错，，它是最终状态 （尽管该作业仍可以 DELETED) 不能更新。 状态属性的一个示例是，如下所示：

        "state": "disabled", // enabled, disabled, completed, or faulted
在 60 天后删除已完成和出错的作业。

## <a name="status"></a>status
启动计划程序作业后，信息将返回有关作业的当前状态。 此对象不是可由用户设置-由系统设置。 但是，它包含在作业对象 （而不是单独的链接的资源），以便可以轻松地获取作业的状态。

作业状态包含前一次执行 （如果有） 的时间、 （对于正在进行的作业） 下一步计划执行的时间和作业的执行计数。

## <a name="retrypolicy"></a>retryPolicy
如果计划程序作业失败，则可以指定重试策略来确定是否以及如何重试操作。 这由**retryType**对象-设置为**无**如果没有重试策略，如上所示。 将其设置为**固定**如果没有重试策略。

若要设置重试策略，可能指定两个附加设置： 重试间隔 (**retryInterval**) 和重试次数 (**retryCount**)。

使用指定的重试间隔**retryInterval**对象，请重试之间的时间间隔。 其默认值为 30 秒，其最小的可配置值为 15 秒，其最大值为 18 个月。 免费作业集合中的作业具有 1 小时的最小的可配置值。  ISO 8601 格式定义它。 同样，指定的重试次数的值与**retryCount**对象; 它是尝试重试次数。 其默认值为 4，，其最大值为 20。 同时**retryInterval**和**retryCount**都是可选的。 它们赋予默认值，如果**retryType**设置为**固定**和显式指定任何值。

## <a name="see-also"></a>另请参阅
 [计划程序是什么？](scheduler-intro.md)

 [开始在 Azure 门户中使用计划程序](scheduler-get-started-portal.md)

 [计划和 Azure 计划程序中的计费](scheduler-plans-billing.md)

 [如何生成复杂的计划和高级重复执行使用 Azure 计划程序](scheduler-advanced-complexity.md)

 [Azure 计划程序 REST API 参考](https://msdn.microsoft.com/library/mt629143)

 [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)

 [Azure 计划程序高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 计划程序的限制、 默认值和错误代码](scheduler-limits-defaults-errors.md)

 [Azure 计划程序出站身份验证](scheduler-outbound-authentication.md)

