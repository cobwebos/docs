---
title: 验证关键短语提取容器实例
titleSuffix: Azure Cognitive Services
description: 了解如何验证关键短语提取容器实例。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 1e2001c1f4cb2da195a3dcd0ca8fe198de8dd264
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968549"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>验证关键短语提取容器实例

1. 选择 "**概览**" 选项卡, 然后复制 IP 地址。
1. 打开新的浏览器选项卡, 并输入 IP 地址。 例如，输入 `http://<IP-address>:5000 (http://55.55.55.55:5000`)。 此时会显示容器的主页, 使你知道容器正在运行。

    ![查看容器主页，以验证它是否处于运行状态](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 选择 "**服务 API 说明**" 链接以前往容器的 Swagger 页面。

1. 选择任何**POST** api, 并选择 "**试用**"。将显示参数, 其中包括以下示例输入:

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

1. 将**showStats**设置`true`为。

1. 选择“执行”以确定文本的情绪。

    容器中打包的模型生成范围为0到1之间的分数, 其中0表示负数, 1 表示正值。

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

我们现在可以将响应有效负载的 JSON 数据的文档 `id` 关联到原始请求有效负载文档 `id`。 生成的文档具有一个`keyPhrases`数组，其中包含已从相应输入文档中提取的关键短语的列表。 此外，每个生成的文档都`characterCount`有`transactionCount`各种统计信息，例如和。