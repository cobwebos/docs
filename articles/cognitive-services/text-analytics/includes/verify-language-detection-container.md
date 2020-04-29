---
title: 验证语言检测容器实例
titleSuffix: Azure Cognitive Services
description: 了解如何验证语言检测容器实例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876390"
---
### <a name="verify-the-language-detection-container-instance"></a>验证语言检测容器实例

1. 选择 "**概览**" 选项卡，然后复制 IP 地址。
1. 打开新的浏览器选项卡，并输入 IP 地址。 例如，输入 `http://<IP-address>:5000 (http://55.55.55.55:5000`)。 此时会显示容器的主页，使你知道容器正在运行。

    ![查看容器主页，以验证它是否处于运行状态](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 选择 "**服务 API 说明**" 链接以前往容器的 Swagger 页面。

1. 选择任何**POST** api，并选择 "**试用**"。将显示参数，其中包括以下示例输入：

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. 将**showStats**设置`true`为。

1. 选择“执行”**** 以确定文本的情绪。

    容器中打包的模型生成范围为0到1之间的分数，其中0表示负情绪，1表示正情绪。

    返回的 JSON 响应包括更新的文本输入的情绪：

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

现在，我们可以按相应`id`的方式将响应负载的 JSON 数据的文档与原始请求负载文档关联。 每个文档独立对待`characterCount` ，其中包含各种统计信息`transactionCount`，例如和。 此外，每个生成的文档`detectedLanguages`都具有每`name`个`iso6391Name`检测到`score`的语言的、和的数组。 检测到多个语言时， `score`将使用来确定最可能的语言。