---
title: Azure Batch 池删除完成事件 | Microsoft Docs
description: 批处理池删除完成事件参考。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: fd32554866d1e2130fd0833adc1b286fb6bc07a5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323225"
---
# <a name="pool-delete-complete-event"></a>池删除完成事件

 当完成池删除操作时，会发出此事件。

 以下示例演示了池删除完成事件的正文。

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|元素|类型|说明|
|-------------|----------|-----------|
|id|String|池的 id。|
|startTime|DateTime|池删除开始的时间。|
|endTime|DateTime|池删除完成的时间。|

## <a name="remarks"></a>备注
有关池调整大小操作的状态和错误代码的详细信息，请参阅[从帐户中删除池](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)。