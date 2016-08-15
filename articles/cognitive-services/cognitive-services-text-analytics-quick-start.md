<properties
	pageTitle="快速入门指南：机器学习文本分析 API | Azure"
	description="Azure 机器学习文本分析 - 快速入门指南"
	services="cognitive-services"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.date="07/05/2016"
	wacn.date=""/>

# 用于检测情绪、关键短语、主题和语言的文本分析 API 入门

<a name="HOLTop">

本文档介绍如何登记你的服务或应用程序，以使用[文本分析 API](https://www.microsoft.com/cognitive-services/zh-cn/text-analytics-api)。你可以使用这些 API 来检测文本中的情绪、关键短语、主题和语言。[单击此处观看交互式体验演示。](https://text-analytics-demo.azurewebsites.net/)

请参阅 [API definitions](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)（API 定义）以获取 API 的技术文档。

本指南适用于第 2 版 API。有关第 1 版 API 的详细信息，请[参阅此文档](/documentation/articles/machine-learning-apps-text-analytics/)。

本教程结束时，你将能够以编程方式检测：

- **情绪** - 文本是积极的还是消极的？

- **关键短语** - 作者在一篇文章中讨论的内容是什么？

- **主题** - 作者在多篇文章中讨论的内容是什么？

- **语言** - 文本是以哪种语言编写的？

请注意，每提交一份文档，此 API 就会收取 1 笔交易费用。例如，如果你在单个调用中请求 1000 份文档，则会扣除 1000 笔交易。



<a name="Overview"></a>
## 一般概述 ##

本文档是一份循序渐进的指南，旨在引导你完成训练模型所要执行的步骤，并提供一些资源的链接，以帮助你将该模型投入生产。本练习耗时大约 30 分钟。

对于这些任务，需要使用一个编辑器，并以选定的语言调用 RESTful 终结点。

让我们开始吧！

## 任务 1 - 注册文本分析 API ####

在此任务中，你将注册文本分析服务。

1. 导航到 [Azure 门户](https://portal.azure.cn)中的“认知服务”，并且确保选择“文本分析”作为“API 类型”。

1. 选择一个计划。你可以选择**免费层，每月可以免费完成 5,000 笔交易**。因为这是一个免费计划，你不需要支付服务使用费。你需要登录到你的 Azure 订阅。

1. 完成其他字段并创建帐户。

1. 注册文本分析后，找到你的 **API 密钥**。复制主密钥，因为使用 API 服务时需要用到它。


## 任务 2 - 检测情绪、关键短语和语言 ####

可以轻松地在文本中检测情绪、关键短语和语言。以编程方式获取[演示体验](https://text-analytics-demo.azurewebsites.net/)所返回的相同结果。

>[AZURE.TIP] 要进行情绪分析，我们建议将文本拆分成句子。这通常可以提高情绪预测的精确度。

请注意，支持的语言如下：

| 功能 | 支持的语言代码 |
|:-----|:----|
| 情绪 | `en`（英语）、`es`（西班牙语）、`fr`（法语）、`pt`（葡萄牙语） |
| 关键短语 | `en`（英语）、`es`（西班牙语）、`de`（德语）、`ja`（日语） |


1. 必须将标头设置为以下内容。请注意，API 目前只接受 JSON 输入格式。不支持 XML。

		Ocp-Apim-Subscription-Key: <your API key>
		Content-Type: application/json
		Accept: application/json

1. 接下来，将输入行设置为 JSON 格式。对于情绪、关键短语和语言，它们的格式相同。请注意，每个 ID 应该是唯一的，并且是系统返回的 ID。可以提交的单个文档大小上限为 10KB，提交的输入总大小上限为 1MB。一个调用中提交的文档不能超过 1,000 个。语言是可选参数，如果要分析非英语文本，则应指定语言。输入的示例如下所示，其中包含用于情绪分析或关键短语提取的可选参数 `language`：

		{
			"documents": [
				{
					"language": "en",
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"language": "en",
					"id": "100",
					"text": "Final document"
				}
			]
		}

1. 使用情绪、关键短语和语言输入对系统执行 **POST** 调用。URL 如下所示：

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. 此调用将返回 JSON 格式的响应，其中包含 ID 和检测到的属性。情绪输出的示例如下所示（不包括错误详细信息）。执行情绪检测时，将针对每个文档返回 0 到 1 分：

        // Sentiment response
		{
		  	"documents": [
				{
					"id": "1",
					"score": "0.934"
		        },
                ...
                {
					"id": "100",
					"score": "0.002"
		        },
			]
		}

        // Key phrases response
        {
		  	"documents": [
				{
					"id": "1",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
                ...
                {
					"id": "100",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
			]
		}

        // Languages response
        {
		  	"documents": [
				{
					"id": "1",
					"detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
		        },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
		        }
			]
		}


## 任务 3 - 在文本主体中检测主题 ####

这是新发布的 API，可针对已提交的文本记录列表返回前几个检测到的主题。主题以关键短语进行标识，可以是一个或多个相关的单词。对于简短的人工书写文本（例如评论和用户反馈），可以正常使用该 API。

此 API 需要提交**至少 100 条文本记录**，但也可以检测数百到数千条记录中的主题。任何非英语记录或者包含的单词少于 3 个的记录都将被丢弃，因此不会分配到主题。执行主题检测时，可以提交的单个文档大小上限为 30KB，提交的输入总大小上限为 30MB。

有两个附加的**可选**输入参数可以帮助改善结果的质量：

- **干扰字。** 将从整个主题检测管道中排除这些单词及其相关形式（例如复数）。请针对常见单词使用此参数（例如，在有关软件的客户投诉中，“issue”、“error”和“user”可能是适当的选择）。每个字符串应该是一个单词。
- **干扰短语** - 将从返回的主题列表中排除这些短语。使用此参数可以排除你不想要在结果中看到的通用主题。例如，“Microsoft”和“Azure”是排除的主题的适当选择。字符串可以包含多个单词。

根据以下步骤检测文本中的主题。

1. 将输入设置为 JSON 格式。此时，你可以定义干扰字和干扰短语。

		{
			"documents": [
				{
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"id": "100",
					"text": "Final document"
				}
			],
			"stopWords": [
				"issue", "error", "user"
			],
			"stopPhrases": [
				"Microsoft", "Azure"
			]
		}

1. 使用任务 2 中定义的相同标头，对主题终结点执行 **POST** 调用：

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. 随后将返回 `operation-location` 作为响应中的标头，其中的值是用于查询最终主题的 URL：

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. 定期使用 **GET** 请求查询返回的 `operation-location`。建议每分钟查询一次。

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. 终结点将返回一个响应，其中包含处理之前的 `{"status": "notstarted"}`、处理时的 `{"status": "running"}` 以及完成后的 `{"status": "succeeded"}` 和输出。然后，你可以使用以下格式的输出（请注意，此示例中未包括错误格式和日期等详细信息）：

		{
			"status": "succeeded",
			"operationProcessingResult": {
			  	"topics": [
                    {
					    "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
					    "score": "5"
					    "keyPhrase": "first topic name"
                    },
                    ...
                    {
					    "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
					    "score": "3"
					    "keyPhrase": "final topic name"
                    }
                ],
			  	"topicAssignments": [
                    {
					    "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
					    "documentId": "1",
					    "distance": "0.354"
                    },
                    ...
                    {
					    "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
					    "documentId": "55",
					    "distance": "0.758"
                    },            
                ]
			}
		}

请注意，针对 `operations` 终结点中的主题返回的成功响应采用以下架构：

	{
    		"topics" : [{
        		"id" : "string",
        		"score" : "number",
        		"keyPhrase" : "string"
    		}],
    		"topicAssignments" : [{
        		"documentId" : "string",
        		"topicId" : "string",
        		"distance" : "number"
    		}],
    		"errors" : [{
        		"id" : "string",
        		"message" : "string"
    		}]
    	}

此响应的每个部分的说明如下：

**topics**

| 键 | 说明 |
|:-----|:----|
| id | 每个主题的唯一标识符。 |
| score | 分配给主题的文档数。 |
| keyPhrase | 主题的摘要单词或短语。 |

**topicAssignments**

| 键 | 说明 |
|:-----|:----|
| documentId | 文档的标识符。等同于输入中包含的 ID。 |
| topicId | 文档分配到的主题 ID。 |
| distance | 介于 0 和 1 之间的文档-主题隶属关系分数。距离分数越低，主题隶属关系越强。 |

**错误**

| 键 | 说明 |
|:-----|:----|
| id | 错误引用的输入文档唯一标识符。 |
| message | 错误消息。 |

## 后续步骤 ##

祝贺你！ 你已完成对数据使用文本分析。现在，你可以考虑使用一种工具（例如 [Power BI](//powerbi.microsoft.com)）以可视化方式呈现数据，以及自动生成见解，以提供文本数据的实时视图。

若要了解如何将文本分析功能（如情绪检测）用作机器人的一部分，请参阅 Bot Framework（机器人框架）站点上的 [Emotional Bot](http://docs.botframework.com/zh-cn/bot-intelligence/language/#example-emotional-bot)（情绪机器人）示例。

<!---HONumber=Mooncake_0808_2016-->