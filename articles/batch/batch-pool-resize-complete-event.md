---
title: Azure Batch 池调整大小完成事件 | Microsoft Docs
description: 批处理池调整大小完成事件参考。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 8c0843db216ff99aabfda9074ee751597b43a2a2
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258407"
---
# <a name="pool-resize-complete-event"></a>池调整大小完成事件

 当池大小调整已完成或失败时，会发出此事件。

 以下示例显示了池的池调整大小完成事件（即大小已增加并且已成功完成）的正文。

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|元素|类型|说明|
|-------------|----------|-----------|
|`id`|String|池的 ID。|
|`nodeDeallocationOption`|String|指定何时从池中删除节点（如果池的大小正在减小）。<br /><br /> 可能的值包括：<br /><br /> **requeue** – 终止正在运行的任务并将其重新排队。 当作业启用时，任务将再次运行。 一旦任务终止，便会立即删除节点。<br /><br /> **terminate** – 终止正在运行的任务。 任务将不会再次运行。 一旦任务终止，便会立即删除节点。<br /><br /> **taskcompletion** – 允许完成当前正在运行的任务。 等待时不计划任何新任务。 在所有任务完成时，删除节点。<br /><br /> **Retaineddata** - 允许完成当前正在运行的任务，并等待所有任务数据保留期到期。 等待时不计划任何新任务。 在所有任务保留期都已过期时，删除节点。<br /><br /> 默认值为 requeue。<br /><br /> 如果池的大小正在增加，该值将设置为**无效**。|
|`currentDedicatedNodes`|Int32|当前分配给池的专用计算节点数。|
|`targetDedicatedNodes`|Int32|为池请求的专用计算节点数。|
|`currentLowPriorityNodes`|Int32|当前分配给池的低优先级计算节点数。|
|`targetLowPriorityNodes`|Int32|池请求的低优先级计算节点数。|
|`enableAutoScale`|Bool|指定池大小是否随时间自动调整。|
|`isAutoPool`|Bool|指定是否已通过作业的 AutoPool 机制创建池。|
|`startTime`|DateTime|池调整大小开始的时间。|
|`endTime`|DateTime|池调整大小完成的时间。|
|`resultCode`|String|调整大小的结果。|
|`resultMessage`|String| 有关结果的详细消息。<br /><br /> 如果调整大小已成功完成，则表示操作成功。|
