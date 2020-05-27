---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791702"
---
### <a name="functions-2x-and-higher"></a>函数1.x 和更高版本

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|properties  |默认 | 说明 |
|---------|---------|---------|
|maxBatchSize|10|每个接收循环收到的最大事件计数。|
|prefetchCount|300|基础`EventProcessorHost`使用的默认预提取计数。|
|batchCheckpointFrequency|1|创建 EventHub 游标检查点之前要处理的事件批数。|

> [!NOTE]
> 有关 Azure Functions 2.x 及更高版本中的 host json 的参考，请参阅[Azure Functions 的 host json 引用](../articles/azure-functions/functions-host-json.md)。

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|properties  |默认 | 说明 |
|---------|---------|---------| 
|maxBatchSize|64|每个接收循环收到的最大事件计数。|
|prefetchCount|n/a|将由基础`EventProcessorHost`使用的默认预提取。| 
|batchCheckpointFrequency|1|创建 EventHub 游标检查点之前要处理的事件批数。| 

> [!NOTE]
> 有关 Azure Functions 1.x 中的 host json 的参考，请参阅 Azure Functions 1.x[的 host json 引用](../articles/azure-functions/functions-host-json-v1.md)。

