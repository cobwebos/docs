---
title: "Azure 批处理池调整大小完成事件 | Microsoft Docs"
description: "批处理池调整大小完成事件参考。"
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7072293d98526812cb42ce9c2f8e33bfcafaa149
ms.lasthandoff: 04/22/2017

---

# <a name="pool-resize-complete-event"></a>池调整大小完成事件

 当池大小调整已完成或失败时，会发出此事件。

 以下示例显示了池的池调整大小完成事件（即大小已增加并且已成功完成）的正文。

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|元素|类型|说明|
|-------------|----------|-----------|
|id|String|池的 id。|
|nodeDeallocationOption|String|指定何时从池中删除节点（如果池的大小正在减小）。<br /><br /> 可能的值包括：<br /><br /> **requeue** – 终止正在运行的任务并将其重新排队。 当作业启用时，任务将再次运行。 一旦任务终止，便会立即删除节点。<br /><br /> **terminate** – 终止正在运行的任务。 任务将不会再次运行。 一旦任务终止，便会立即删除节点。<br /><br /> **taskcompletion** – 允许完成当前正在运行的任务。 等待时不计划任何新任务。 在所有任务完成时，删除节点。<br /><br /> **Retaineddata** - 允许完成当前正在运行的任务，然后等待所有任务数据保留期到期。 等待时不计划任何新任务。 在所有任务保留期都已过期时，删除节点。<br /><br /> 默认值为 requeue。<br /><br /> 如果池的大小正在增加，该值将设置为**无效**。|
|currentDedicated|Int32|当前分配到池的计算节点数。|
|targetDedicated|Int32|池请求的计算节点数。|
|enableAutoScale|Bool|指定池大小是否随时间自动调整。|
|isAutoPool|Bool|指定是否已通过作业的 AutoPool 机制创建池。|
|startTime|DateTime|池调整大小开始的时间。|
|endTime|DateTime|池调整大小完成的时间。|
|resultCode|String|调整大小的结果。|
|resultMessage|String|调整大小错误包括结果的详细信息。<br /><br /> 如果调整大小已成功完成，则表示操作成功。|
