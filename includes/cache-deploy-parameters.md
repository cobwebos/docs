---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132821"
---
### <a name="cacheskuname"></a>cacheSKUName

新 Azure Redis 缓存的定价层。

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

该模板定义 （基本、 标准或高级），此参数允许的值，如果未不指定任何值分配默认值 (Basic)。 Basic 提供单个节点，该节点具有多种大小，最大大小为 53 GB。 Standard 提供“主/副本”两个节点，这些节点具有多种大小（最大 53 GB）并提供 99.9% SLA。

### <a name="cacheskufamily"></a>cacheSKUFamily

SKU 的系列。

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

新 Azure Redis 缓存实例的大小。

有关的基本和标准的系列：

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

高级值缓存容量定义相同的除了允许的值从 1 运行至 5 而不是从 0 到 6。

模板定义此参数 （0 到 6 个用于基本和标准系列; 1 至 5 高级家族） 允许的整数值。 如果未不指定任何值，该模板将基本和标准版、 高级版 1 分配默认值为 0。

值对应于以下缓存大小：

| 值 | 基本和标准<br>缓存大小 | 高级<br>缓存大小 |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB （默认值）                 | 不适用                   |
| 第     | 1 GB                             | 6 GB （默认值）        |
| 2     | 2.5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | 不适用                   |
