---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 1b70c5e71c585524579171782dfdeda308592b30
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478674"
---
若要了解 LUIS 预测终结点返回的内容，请在 Web 浏览器中查看预测结果。 若要查询公共应用，需要自己密钥和应用 ID。 公共 IoT 应用 ID (`df67dcdb-c37d-46af-88e1-8b97951ca1c2`) 在步骤 1 中作为 URL 的一部分提供。

**GET** 终结点请求的 URL 格式为：

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. 公共 IoT 应用的终结点采用以下格式：`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    复制 URL 并将 `<YOUR_KEY>` 的值替换为自己的密钥。

2. 将该 URL 粘贴到浏览器窗口中，然后按 Enter。 浏览器显示的 JSON 结果指示 LUIS 将 `HomeAutomation.TurnOn` 意向检测为首要意向，并检测到值为 `bedroom` 的 `HomeAutomation.Room` 实体。

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. 将 URL 中的 `q=` 参数值更改为 `turn off the living room light`，然后按 Enter。 现在，结果指示 LUIS 将 `HomeAutomation.TurnOff` 意向检测为首要意向，并检测到值为 `living room` 的 `HomeAutomation.Room` 实体。 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
