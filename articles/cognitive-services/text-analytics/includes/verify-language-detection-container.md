---
title: 验证语言检测容器实例
titleSuffix: Azure Cognitive Services
description: 了解如何验证语言检测容器实例。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: f4e0770bc052044a408f2c4088f2bd5ead225aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968548"
---
### <a name="verify-the-language-detection-container-instance"></a>验证语言检测容器实例

1. 选择 **"概述"** 选项卡，然后复制 IP 地址。
1. 打开新的浏览器选项卡，然后输入 IP 地址。 例如，输入 `http://<IP-address>:5000 (http://55.55.55.55:5000`)。 将显示容器的主页，让您知道容器正在运行。

    ![查看容器主页，以验证它是否处于运行状态](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 选择 **"服务 API 描述"** 链接以转到容器的 Swagger 页面。

1. 选择任选**点**API，然后选择 **"试用"。** 将显示参数，其中包括此示例输入：

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

1. 将 **"显示"设置**为`true`。

1. 选择“执行”**** 以确定文本的情绪。

    包装在容器中的模型生成介于 0 到 1 的分数，其中 0 为负情绪，1 为正情绪。

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

我们现在可以通过相应的将响应有效负载的 JSON 数据的文档与其原始请求有效负载文档相关联`id`。 每个文档都单独处理，其中包含各种统计信息，`characterCount`如`transactionCount`和 。 此外，每个`detectedLanguages`生成的文档都有包含 的`name`数组`iso6391Name`，每个`score`语言都检测到 。 当检测到多种语言时，`score`用于确定最可能的语言。