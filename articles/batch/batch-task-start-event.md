---
title: Azure Batch 任务开始事件
description: Batch 任务开始事件的参考信息。 当任务已计划由计划程序在计算节点上启动时，会发出此事件。
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: f872e6c89e2f528729ae08125c6cc3eb5718e19a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725782"
---
# <a name="task-start-event"></a>任务开始事件

 当任务已计划由计划程序在计算节点上启动时，会发出此事件。 请注意，如果重试任务或对其进行重新排队，此事件将再次发出，但重试计数和系统任务版本将相应更新。


 以下示例显示任务开始事件的正文。

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|元素名称|类型|说明|
|------------------|----------|-----------|
|`jobId`|String|包含任务的作业的 ID。|
|`id`|String|任务的 ID。|
|`taskType`|String|任务的类型。 它可以是“JobManager”（指示它是作业管理器任务），也可以是“User”（指示它并非作业管理器任务）。|
|`systemTaskVersion`|Int32|这是任务上的内部重试计数器。 批处理服务可能会在内部重试任务来解决暂时性问题。 这些问题可能包括内部计划错误或尝试恢复处于错误状态的计算节点。|
|[`nodeInfo`](#nodeInfo)|复杂类型|包含有关运行任务的计算节点的信息。|
|[`multiInstanceSettings`](#multiInstanceSettings)|复杂类型|指定任务是需要多个计算节点的多实例任务。  有关详细信息，请参阅 [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task)。|
|[`constraints`](#constraints)|复杂类型|应用到此任务的执行约束。|
|[`executionInfo`](#executionInfo)|复杂类型|包含有关任务执行的信息。|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|元素名称|类型|说明|
|------------------|----------|-----------|
|`poolId`|String|运行任务的池的 ID。|
|`nodeId`|String|运行任务的节点的 ID。|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|元素名称|类型|说明|
|------------------|----------|-----------|
|`numberOfInstances`|Int|任务所需的计算节点数。|

###  <a name="constraints"></a><a name="constraints"></a> constraints

|元素名称|类型|说明|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|可以重试任务的最大次数。 批处理服务在其退出代码非零时重试任务。<br /><br /> 请注意，此值专门用于控制重试的次数。 批处理服务将尝试任务一次，然后重试，直至达到此上限为止。 例如，如果最大重试计数为 3，则批处理任务最多尝试任务 4 次（一次是初始尝试，其余 3 次是重试）。<br /><br /> 如果最大重试计数为 0，则批处理服务不会重试任务。<br /><br /> 如果最大重试计数为 -1，则批处理服务会无限制地重试任务。<br /><br /> 默认值为 0（不重试）。|

###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|元素名称|类型|说明|
|------------------|----------|-----------|
|`retryCount`|Int32|批处理服务重试任务的次数。 如果任务使用非零退出代码退出，该任务会重试，直至达到指定的 MaxTaskRetryCount|
