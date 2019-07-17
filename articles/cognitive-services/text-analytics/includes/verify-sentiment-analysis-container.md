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
ms.openlocfilehash: f69d573e9e70a505018e94cca354f363097cc1b8
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229172"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>验证情绪分析容器实例

1. 选择**概述**选项卡，复制的 IP 地址。
1. 打开新浏览器选项卡并输入 IP 地址。 例如，输入`http://<IP-address>:5000 (http://55.55.55.55:5000`)。 显示容器的主页上，如果希望了解容器正在运行。

    ![查看容器主页上，验证正在运行](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. 选择**服务 API 说明**链接以转到容器的 swagger 页。

1. 选择任一**POST** Api，然后选择**试试看**。这些参数会显示，其中包括此示例输入：

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

1. 输入替换为以下 JSON 内容：

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

1. 设置**showStats**为 true。

1. 选择**Execute**来确定文本的情绪。

    在容器中打包模型生成分数范围从 0 到 1，其中 0 表示负数，1 为正。

    返回的 JSON 响应包括更新后输入的文本的情绪：

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

我们现在可以关联文档`id`的响应有效负载的 JSON 数据的原始请求有效负载文档`id`。 我们看到的分数为多个`.98`，指示强积极的情绪。