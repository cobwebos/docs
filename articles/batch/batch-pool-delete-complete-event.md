---
title: Azure Batch 池删除完成事件
description: 批处理池删除完成事件参考。 当完成池删除操作时，会发出此事件。
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d317d7395a8246c109073a72338b55960cb50954
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023610"
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
|`id`|String|池的 ID。|
|`startTime`|日期/时间|池删除开始的时间。|
|`endTime`|日期/时间|池删除完成的时间。|

## <a name="remarks"></a>备注
有关池调整大小操作的状态和错误代码的详细信息，请参阅[从帐户中删除池](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)。
