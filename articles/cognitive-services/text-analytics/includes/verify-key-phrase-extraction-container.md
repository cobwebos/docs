---
title: 验证关键短语提取容器实例
titleSuffix: Azure Cognitive Services
description: 了解如何验证关键短语提取容器实例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876388"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>验证关键短语提取容器实例

1. 选择 **"概述"** 选项卡，然后复制 IP 地址。
1. 打开新的浏览器选项卡，然后输入 IP 地址。 例如，输入 `http://<IP-address>:5000 (http://55.55.55.55:5000`)。 将显示容器的主页，让您知道容器正在运行。

    ![查看容器主页，以验证它是否处于运行状态](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 选择 **"服务 API 描述"** 链接以转到容器的 Swagger 页面。

1. 选择任选**点**API，然后选择 **"试用"。** 将显示参数，其中包括此示例输入：

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. 将该输入替换为以下 JSON 内容：

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. 将 **"显示"设置**为`true`。

1. 选择“执行”**** 以确定文本的情绪。

    打包在容器中的模型生成介于 0 到 1 的分数，其中 0 为负，1 为正。

    返回的 JSON 响应包括更新的文本输入的情绪：

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

我们现在可以将响应有效负载的 JSON 数据的文档 `id` 关联到原始请求有效负载文档 `id`。 生成的文档具有一个`keyPhrases`数组，其中包含从相应的输入文档中提取的关键短语的列表。 此外，还有各种统计信息，例如`characterCount`和`transactionCount`每个生成的文档。