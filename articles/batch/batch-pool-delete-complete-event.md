---
title: Azure Batch 池删除完成事件
description: 批处理池删除完成事件参考。 当完成池删除操作时，会发出此事件。
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: f00d32fd5e4eb49fddf4975a9b64ce792d0226dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85962452"
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
|`startTime`|DateTime|池删除开始的时间。|
|`endTime`|DateTime|池删除完成的时间。|

## <a name="remarks"></a>备注
有关池调整大小操作的状态和错误代码的详细信息，请参阅[从帐户中删除池](/rest/api/batchservice/delete-a-pool-from-an-account)。
