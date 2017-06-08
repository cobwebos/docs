---
title: "机器学习 API：文本分析 | Microsoft Docs"
description: "Microsoft 机器学习文本分析 API 可用于分析非结构化文本，以进行情绪分析、关键短语提取、语言检测和主题检测。"
services: machine-learning
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: 5b60694e-5521-4e4d-bf6a-1a92fdf94b65
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: onewth
ROBOTS: NOINDEX
redirect_url: ../cognitive-services/cognitive-services-text-analytics-quick-start
redirect_document_id: TRUE
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f4389705a81b531bd706cbabc0b4c3b171febd5f
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016


---
# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>机器学习 API：用于情绪、关键短语提取、语言检测和主题检测的文本分析
> [!NOTE]
> 本指南适用于第 1 版 API。 关于版本 2，请[**本文档**](../cognitive-services/cognitive-services-text-analytics-quick-start.md)。 现在，版本 2 是此 API 的首选版本。
> 
> 

## <a name="overview"></a>概述
文本分析 API 是一套使用 Azure 机器学习构建的文本分析 [Web 服务](https://datamarket.azure.com/dataset/amla/text-analytics)。 API 可用于分析非结构化文本，以执行如情绪分析、关键短语提取、语言检测和主题检测等任务。 使用此 API 无需任何定型数据：只需文本数据。 此 API 使用高级自然语言处理技术提供最佳的类预测。

可以在[演示站点](https://text-analytics-demo.azurewebsites.net/)上查看文字分析操作，还可以在这里找到有关如何在 C# 和 Python 中实现文本分析的[实例](https://text-analytics-demo.azurewebsites.net/Home/SampleCode)。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

- - -
## <a name="sentiment-analysis"></a>情绪分析
API 返回 0 和 1 之间的数值分数。 接近 1 的分数表示积极情绪，而接近 0 的分数表示消极情绪。 使用分类技术生成情绪分数。 分类器的输入特征包括 n 元语法，从词性标记生成的特征以及单词嵌入。 目前，英语是唯一受支持的语言。

## <a name="key-phrase-extraction"></a>关键短语提取
API 返回表示输入文本中的关键话题的字符串列表。 我们使用 Microsoft Office 的复杂自然语言处理工具包技术。 目前，英语是唯一受支持的语言。

## <a name="language-detection"></a>语言检测
API 返回检测到的语言以及 0 和 1 之间的数值分数。 接近 1 的分数表示 100% 确定所识别的语言是真实的。 共支持 120 种语言。

## <a name="topic-detection"></a>主题检测
这是新发布的 API，它返回提交的文本记录列表中最常检测到的主题。 主题以关键短语进行标识，可以是一个或多个相关的单词。 此 API 要求至少提交 100 个文本记录，但旨在从几百到几千条记录中检测主题。 请注意，每提交一份文本记录，此 API 就会收取 1 笔交易费用。 对于简短的人工书写文本（例如评论和用户反馈），可以正常使用该 API。

- - -
## <a name="api-definition"></a>API 定义
### <a name="headers"></a>标头
确保请求包含正确的标头，如下所示：

    Authorization: Basic <creds>
    Accept: application/json

    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

可以在 [Azure 数据市场](https://datamarket.azure.com/account/keys)帐户中找到帐户密钥。 请注意，当前仅接受 JSON 用于输入和输出格式。 不支持 XML。

- - -
## <a name="single-response-apis"></a>单个响应 API
### <a name="getsentiment"></a>GetSentiment
**URL**    

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**示例请求**

在下面的调用中，请求短语“Hello World”的情绪分析：

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

这将返回一个响应，如下所示：

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

- - -
### <a name="getkeyphrases"></a>GetKeyPhrases
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**示例请求**

在下面的调用中，请求在文本“这是一家极好的酒店，装潢独特，工作人员也很友好”中找到的关键短语：

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

这将返回一个响应，如下所示：

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }

- - -
### <a name="getlanguage"></a>GetLanguage
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**示例请求**

在下面的 GET 调用中，请求文本“Hello World”中关键短语的情绪

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

这将返回一个响应，如下所示：

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**可选参数**

`NumberOfLanguagesToDetect` 是一个可选参数。 默认值为 1。

- - -
## <a name="batch-apis"></a>批处理 API
文本分析服务允许在批处理模式下进行情绪和关键短语提取。 请注意，每个评分的记录都计作一笔交易。 例如，如果在单个调用中请求 1000 份记录，则会扣除 1000 笔交易。

请注意，输入系统的 ID 是系统返回的ID。 Web 服务不会检查这些 ID 是否唯一的。 调用者负责验证唯一性。 

### <a name="getsentimentbatch"></a>GetSentimentBatch
**URL**    

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**示例请求**

在下面的 POST 调用中，请求关于请求正文中的短语“Hello World”、“Hello Foo World”和“Hello My World”的情绪：

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

请求正文：

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

在下面的响应中，可获得与文本 ID 相关联的分数列表：

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


- - -
### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**示例请求**

在此示例中，请求下列文本中关键短语的情绪列表： 

* “这是一家极好的酒店，装潢独特，工作人员也很友好”
* “这是一次非常棒的建筑会议，话题非常有趣”
* “路上太堵了，我花了三个小时才到达机场”

此请求作为对终结点的 POST 调用发出：

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

请求正文：

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

在下面的响应中，获取与文本 ID 相关联的关键短语列表：

    { "odata.metadata":"<url>",
         "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

- - -
### <a name="getlanguagebatch"></a>GetLanguageBatch

在下面的 POST 调用中，请求两个文本输入的语言检测：

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

请求正文：

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

这将返回以下响应，其中在第一个输入中检测到英语，在第二个输入中检测为法语：

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

- - -
## <a name="topic-detection-apis"></a>主题检测 API
这是新发布的 API，它返回提交的文本记录列表中最常检测到的主题。 主题以关键短语进行标识，可以是一个或多个相关的单词。 请注意，每提交一份文本记录，此 API 就会收取 1 笔交易费用。

此 API 要求至少提交 100 个文本记录，但旨在从几百到几千条记录中检测主题。

### <a name="topics--submit-job"></a>主题 – 提交作业
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**示例请求**

在下面的 POST 调用中，请求一组 100 篇文章的主题，其中显示第一篇和最后一篇输入文章，并包括两个 StopPhrases。

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

请求正文：

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

在下面的响应中，获取已提交的作业的 JobId：

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

不应作为主题返回的单个词或多个词短语的列表。 可用于筛选出非常通用的主题。 例如，在关于酒店点评的数据集中，“酒店”和“旅社”可能是合理的驻停短语。  

### <a name="topics--poll-for-job-results"></a>主题 – 作业结果轮询
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**示例请求**

传递从“提交作业”步骤返回的 JobId 以提取结果。 建议每分钟调用一次此终结点，直到响应中出现 Status ='Complete'。 作业完成大约需要 10 分钟，对于具有数千个记录的作业所需时间更长。

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


处理过程中，响应如下：

    {
        "odata.metadata":"<url>",
        "Status":"Running",
         "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


API 以 JSON 格式返回输出，格式如下：

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
          ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


响应的每个部分的属性如下：

**TopicInfo 属性**

| 键 | 说明 |
|:--- |:--- |
| TopicId |每个主题的唯一标识符。 |
| 得分 |分配给主题的记录计数。 |
| KeyPhrase |主题的总结词或短语。 可以是 1 个或多个词。 |

**TopicAssignment 属性**

| 键 | 说明 |
|:--- |:--- |
| ID |记录的标识符。 等同于输入中包含的 ID。 |
| TopicId |已为其分配记录的主题 ID。 |
| 距离 |对记录属于主题的置信度。 距离越接近零表示置信度越高。 |


