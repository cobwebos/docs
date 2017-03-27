---
title: "升级到第 2 版文本分析 API | Microsoft Docs"
description: "Azure 机器学习文本分析 - 升级到第 2 版"
services: cognitive-services
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: bbf86f80-f677-42f3-8c17-118b16a23c34
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: onewth
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9b28666e384402935ae342788afff6b6e8c7eab8


---
# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>升级到第 2 版文本分析 API
本指南指导你完成将使用[第一版 API](../machine-learning/machine-learning-apps-text-analytics.md) 的代码升级为使用第二版 API 的过程。 

如果你未曾用过 API 并想要了解详细信息，可以**[在此处详细了解该 API](//go.microsoft.com/fwlink/?LinkID=759711)**，或**[遵循快速入门指南](//go.microsoft.com/fwlink/?LinkID=760860)**。 有关技术参考，请参阅 **[API Definition](//go.microsoft.com/fwlink/?LinkID=759346)**（API 定义）。

### <a name="part-1-get-a-new-key"></a>第 1 部分： 获取新密钥
首先需要从 **Azure 门户**获取新的 API 密钥：

1. 通过 [Cortana 智能库](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2)导航到文本分析服务。 在此处，你还可以找到文档和代码示例的链接。
2. 单击“**注册**”。 此链接会将你转到 Azure 管理门户，你可以在其中注册该服务。
3. 选择一个计划。 你可以选择**免费层，每月可以免费完成 5,000 笔交易**。 因为这是一个免费计划，你不需要支付服务使用费。 你需要登录到你的 Azure 订阅。 
4. 注册文本分析后，你将会获得 **API 密钥**。 复制此密钥，因为使用 API 服务时需要用到它。

### <a name="part-2-update-the-headers"></a>第 2 部分： 更新标头
更新已提交的标头值，如下所示。 请注意，帐户密钥已不再编码。

**第 1 版**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**第 2 版**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>第 3 部分： 更新基 URL
**第 1 版**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**第 2 版**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>第 4a 部分： 更新情绪、关键短语和语言的格式
#### <a name="endpoints"></a>终结点
GET 终结点现已过时，因此所有输入应以 POST 请求的形式提交。 将终结点更新为如下所示的终结点。

|  | 第 1 版单一终结点 | 第 1 版批处理终结点 | 第 2 版终结点 |
| --- | --- | --- | --- |
| 调用类型 |GET |POST |POST |
| 情绪 |```GetSentiment``` |```GetSentimentBatch``` |```sentiment``` |
| 关键短语 |```GetKeyPhrases``` |```GetKeyPhrasesBatch``` |```keyPhrases``` |
| 语言 |```GetLanguage``` |```GetLanguageBatch``` |```languages``` |

#### <a name="input-formats"></a>输入格式
请注意，现在仅接受 POST 格式，因此应该相应地重新格式化以前使用单一文档终结点的所有输入。 输入不区分大小写。

**第 1 版（批处理）**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**第 2 版**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>情绪输出
**第 1 版**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2 版**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>关键短语输出
**第 1 版**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2 版**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>语言输出
**第 1 版**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2 版**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>第 4b 部分： 更新主题的格式
#### <a name="endpoints"></a>终结点
|  | 第 1 版终结点 | 第 2 版终结点 |
| --- | --- | --- |
| 提交以进行主题检测 (POST) |```StartTopicDetection``` |```topics``` |
| 获取主题结果 (GET) |```GetTopicDetectionResult?JobId=<jobId>``` |```operations/<operationId>``` |

#### <a name="input-formats"></a>输入格式
**第 1 版**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**第 2 版**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>提交结果
**第 1 版 (POST)**

以前，当作业完成时，你会收到以下 JSON 输出，其中的 jobId 将附加到 URL 以用于提取输出。

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**第 2 版 (POST)**

响应现在包含标头值，如下所示，其中使用 `operation-location` 作为终结点来轮询结果：

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>操作结果
**第 1 版 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2 版 (GET)**

与前面一样，将**定期轮询输出**（建议的周期为每隔一分钟），直到返回输出。 

完成主题 API 后，将返回状态读数 `succeeded`。 然后以如下所示格式包含输出结果：

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>第 5 部分： 测试代码！
现在你应已准备就绪！ 结合一个简单示例测试你的代码，以确保可以成功处理数据。




<!--HONumber=Nov16_HO3-->


