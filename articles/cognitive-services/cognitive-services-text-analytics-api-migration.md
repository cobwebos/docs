<properties
	pageTitle="升级到第 2 版文本分析 API | Azure"
	description="Azure 机器学习文本分析 - 升级到第 2 版"
	services="cognitive-services"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.date="07/05/2016"
	wacn.date=""/>

# 升级到第 2 版文本分析 API #

本指南指导你完成将使用[第一版 API](/documentation/articles/machine-learning-apps-text-analytics/) 的代码升级为使用第二版 API 的过程。

如果你未曾用过 API 并想要了解详细信息，可以**[在此处详细了解该 API](https://www.microsoft.com/cognitive-services/zh-cn/text-analytics-api)**，或**[遵循快速入门指南](/documentation/articles/cognitive-services-text-analytics-quick-start/)**。有关技术参考，请参阅 **[API Definition](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)**（API 定义）。

### 第 1 部分：获取新密钥 ###

首先需要从 **Azure 门户**获取新的 API 密钥：

1. 通过 [Cortana 智能库](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2)导航到文本分析服务。在此处，你还可以找到文档和代码示例的链接。

1. 单击“注册”。此链接会将你转到 Azure 管理门户，你可以在其中注册该服务。

1. 选择一个计划。你可以选择**免费层，每月可以免费完成 5,000 笔交易**。因为这是一个免费计划，你不需要支付服务使用费。你需要登录到你的 Azure 订阅。

1. 注册文本分析后，你将会获得 **API 密钥**。复制此密钥，因为使用 API 服务时需要用到它。

### 第 2 部分：更新标头 ###

更新已提交的标头值，如下所示。请注意，帐户密钥已不再编码。

**版本 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**版本 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### 第 3 部分：更新基 URL ###

**版本 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**版本 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### 第 4a 部分：更新情绪、关键短语和语言的格式 ###

#### 终结点 ####

GET 终结点现已过时，因此所有输入应以 POST 请求的形式提交。将终结点更新为如下所示的终结点。

| |第 1 版单一终结点|第 1 版批处理终结点|第 2 版终结点|
|---|---|---|---|
|调用类型|GET|POST|POST|
|情绪|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|关键短语|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|语言|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### 输入格式 ####

请注意，现在仅接受 POST 格式，因此应该相应地重新格式化以前使用单一文档终结点的所有输入。输入不区分大小写。

**第 1 版（批处理）**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**版本 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### 情绪输出 ####

**版本 1**

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

**版本 2**

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

#### 关键短语输出 ####

**版本 1**

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

**版本 2**

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

#### 语言输出 ####


**版本 1**

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

**版本 2**

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


### 第 4b 部分：更新主题的格式 ###

#### 终结点 ####

| |第 1 版终结点 | 第 2 版终结点|
|---|---|---|
|提交以进行主题检测 (POST)|```StartTopicDetection```|```topics```|
|提取主题结果 (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### 输入格式 ####

**版本 1**

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

**版本 2**

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

#### 提交结果 ####

**第 1 版 (POST)**

以前，当作业完成时，你会收到以下 JSON 输出，其中的 jobId 将附加到 URL 以用于提取输出。

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**第 2 版 (POST)**

响应现在包含标头值，如下所示，其中使用 `operation-location` 作为终结点来轮询结果：

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### 操作结果 ####

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

主题 API 完成时，将返回 `succeeded` 状态。然后以如下所示格式包含输出结果：

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

### 第 5 部分：测试代码！ ###

现在你应已准备就绪！ 结合一个简单示例测试你的代码，以确保可以成功处理数据。

<!---HONumber=Mooncake_0808_2016-->