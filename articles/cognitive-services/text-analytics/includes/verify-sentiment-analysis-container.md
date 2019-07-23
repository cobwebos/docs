---
title: 验证情绪分析容器实例
titleSuffix: Azure Cognitive Services
description: 了解如何验证情绪分析容器实例。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1303d753b1cbfabe7ddd3442e0880b0bffe089b3
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377389"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>验证情绪分析容器实例

1. 选择 "**概览**" 选项卡, 然后复制 IP 地址。
1. 打开新的浏览器选项卡, 并输入 IP 地址。 例如, 输入`http://<IP-address>:5000 (http://55.55.55.55:5000`)。 此时会显示容器的主页, 使你知道容器正在运行。

    ![查看容器主页, 验证其是否正在运行](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. 选择 "**服务 API 说明**" 链接以前往容器的 Swagger 页面。

1. 选择任何**POST** api, 并选择 "**试用**"。将显示参数, 其中包括以下示例输入:

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

1. 将输入替换为以下 JSON 内容:

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

1. 将“showStats”  设置为 true。

1. 选择“执行”  以确定文本的情绪。

    容器中打包的模型生成范围为0到1之间的分数, 其中0表示负数, 1 表示正值。

    返回的 JSON 响应包含更新后的文本输入的情绪:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
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

现在, 我们可以将响应`id`负载的 JSON 数据的文档关联到原始请求负载文档。 `id` 超过的`.98`分数表示强烈的情绪。