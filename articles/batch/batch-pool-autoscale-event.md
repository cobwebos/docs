---
title: Azure Batch 池自动缩放事件
description: Batch 池自动缩放事件的参考，一旦执行池自动缩放，就会发出此事件。 日志内容将公开池的自动缩放公式和计算结果。
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91852107"
---
# <a name="pool-autoscale-event"></a>池自动缩放事件

 执行池自动缩放后，将发出此事件。 日志内容将公开池的自动缩放公式和计算结果。

 下面的示例显示了池自动缩放的池自动缩放事件的正文，该自动缩放因样本数据不足而失败。

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|元素|类型|说明|
|-------------|----------|-----------|
|`id`|字符串|池的 ID。|
|`timestamp`|DateTime|执行自动缩放时的时间戳。|
|`formula`|字符串|为自动缩放定义的公式。|
|`results`|字符串|公式中使用的所有变量的计算结果。|
|[`error`](#error)|复杂类型|自动缩放的详细错误。|

###  <a name="error"></a><a name="error"></a> 个错误

|元素名称|类型|说明|
|------------------|----------|-----------|
|`code`|字符串|自动缩放错误的标识符。 代码是固定的，旨在以编程方式使用。|
|`message`|字符串|描述自动缩放错误的消息，旨在适合在用户界面中显示。|
|`values`|Array|描述自动缩放错误的更多详细信息的名称/值对列表。|
