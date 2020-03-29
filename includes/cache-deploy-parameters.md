---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67172759"
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

模板定义此参数允许的值（基本值、标准值或高级值），如果未指定值，则分配默认值（基本值）。 Basic 提供单个节点，该节点具有多种大小，最大大小为 53 GB。 Standard 提供“主/副本”两个节点，这些节点具有多种大小（最大 53 GB）并提供 99.9% SLA。

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

对于基本和标准系列：

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

高级值缓存容量的定义相同，但允许的值从 1 到 5 而不是从 0 到 6 运行。

该模板定义此参数允许的整数值（基本和标准族为 0 到 6;高级族为 1 到 5）。 如果未指定值，模板将为基本值和标准值分配默认值 0，1 表示高级值。

这些值对应于以下缓存大小：

| “值” | 基本和标准<br>缓存大小 | Premium<br>缓存大小 |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB（默认）                 | 不适用                   |
| 1     | 1 GB                             | 6 GB（默认）        |
| 2     | 2.5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | 不适用                   |
