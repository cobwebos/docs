---
title: V3 API 查询字符串参数
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 47727f6df772669fa323a10cd099764a6d35cb6a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610773"
---
V3 API 查询字符串参数包括：

|查询参数|LUIS 门户名称|类型|版本|默认|目的|
|--|--|--|--|--|--|
|`log`|保存日志|boolean|V2 和 V3|false|将查询存储在日志文件中。 默认值为 false。|
|`query`|-|字符串|仅 V3|无默认值 - 在 GET 请求中是必需的|**在 V2 中**，要预测的言语位于 `q` 参数中。 <br><br>**在 V3 中**，该功能在 `query` 参数中传递。|
|`show-all-intents`|包括所有意图的分数|boolean|仅 V3|false|在 **prediction.intents** 对象中返回包含相应评分的所有意向。 意向将在父 `intents` 对象中作为对象返回。 这样，便可以通过编程方式进行访问，而无需在数组中查找意向：`prediction.intents.give`。 在 V2 中，这些意向在数组中返回。 |
|`verbose`|包括更多实体详细信息|boolean|V2 和 V3|false|**在 V2 中**，如果设置为 true，则返回所有预测意向。 如果需要所有预测的意向，请使用 V3 参数 `show-all-intents`。<br><br>**在 V3 中**，此参数仅提供实体预测的实体元数据详细信息。  |
|`timezoneOffset`|-|字符串|V2|-|应用于 datetimeV2 实体的时区。|
|`datetimeReference`|-|string|V3|-|应用于 datetimeV2 实体的[时区](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 替换 V2 中的 `timezoneOffset`。|