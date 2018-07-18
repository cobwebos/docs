---
title: 如何在文本分析 REST API（Azure 上的 Microsoft 认知服务）中进行情绪分析 | Microsoft Docs
description: 在本演练教程中，了解如何使用 Azure 上 Microsoft 认知服务中的文本分析 REST API 检测情绪。
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 12/11/2017
ms.author: heidist
ms.openlocfilehash: 7ffd8bbe47409b459fdd308cd8d670d32f56649b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365679"
---
# <a name="how-to-detect-sentiment-in-text-analytics"></a>如何在文本分析中检测情绪

[情绪分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) 评估每个文档的文本输入并返回情绪分数，分数范围从 0（消极）到 1（积极）。

此功能对于检测社交媒体、客户评论和论坛中的积极和消极情绪非常有用。 用户提供内容；服务提供模型和培训数据。

目前，情绪分析支持英语、德语、西班牙语和法语。 其他语言以预览版提供。 有关详细信息，请参阅[支持的语言](../text-analytics-supported-languages.md)。

## <a name="concepts"></a>概念

文本分析使用机器学习分类算法生成介于 0 到 1 之间的情绪分数。 接近 1 的评分表示积极情绪，接近 0 的评分表示消极情绪。 该模型通过大量含有情感关联的文本进行了预先训练。 目前，不支持提供用户自己的培训数据。 该模型在文本分析过程中使用了多种技术，包括文本处理、词性分析、词序和字词关联。 有关该算法的详细信息，请参阅[文本分析简介](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/)。

对整个文档执行情绪分析，而不是提取文本中特定实体的情绪。 实际上，当文档只包含一个或两个句子而不是大段文本时，评分准确性有提高的趋势。 在客观性评估阶段，模型会确定整个文档是客观内容还是包含情感。 客观内容占主导的文档将不会进入情绪检测阶段，将获得 .50 分数，不会进行进一步处理。 对于继续处理的文档，下一阶段会得出高于或低于 .50 的分数，具体取决于文档中检测到的情绪程度。

## <a name="preparation"></a>准备工作

当为情绪分析提供较小的文本块时，会得到更高质量的结果。 这与关键短语提取相反，关键短语提取在处理较大的文本块时表现更好。 若要从两个操作获取最佳结果，请考虑相应地重建输入。

必须拥有以下格式的 JSON 文档：ID、文本、语言

每个文档的大小必须少于 5,000 个字符，每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。 下面是可能提交用于情绪分析的内容示例。

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>步骤 1：构造请求

有关请求定义的详细信息，请参阅[如何调用文本分析 API](text-analytics-how-to-call-api.md)。 为方便起见，特重申以下几点：

+ 创建 POST 请求。 请查看此请求的 API 文档：[情绪分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ 设置关键短语提取的 HTTP 终结点。 它必须包含 `/sentiment` 资源：`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`

+ 设置请求头以包含文本分析操作的访问密钥。 有关详细信息，请参阅[如何查找终结点和访问密钥](text-analytics-how-to-access-key.md)。

+ 在请求正文中，提供为此分析准备的 JSON 文档集合。

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或打开[文档](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)中的 API 测试控制台来构造请求并将其 POST 到该服务。

## <a name="step-2-post-the-request"></a>步骤 2：发布请求

在收到请求时执行分析。 该服务每分钟最多接受 100 个请求。 每个请求最大为 1 MB。

记住，该服务是无状态服务。 帐户中未存储任何数据。 结果会立即在响应中返回。


## <a name="step-3-view-results"></a>步骤 3：查看结果

情绪分析器将文本分类为积极为主或消极为主，并分配范围在 0 到 1 之间的分数。 接近 0.5 的值表示中性或不确定。 得分 0.5 表示中性。 如果无法分析字符串的情绪或不含情绪，则分数始终为 0.5。 例如，如果传入带有英语语言代码的西班牙语字符串，则分数为 0.5。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中，然后将其导入到允许对数据进行排序、搜索和操作的应用程序。

下方示例展示了本文中文档集合的响应。

```
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="summary"></a>摘要

本文介绍了使用认知服务中的文本分析进行情绪分析的概念和工作流。 综上所述：

+ [情绪分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) 适用于选定的语言。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求的目标是 `/sentiment` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](text-analytics-how-to-access-key.md)。
+ 响应输出包含每个文档 ID 的情绪得分，可流式传输到接受 JSON 的任何应用，包括 Excel 和 Power BI（仅举几例）。

## <a name="see-also"></a>另请参阅 

 [文本分析概述](../overview.md)  
 [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [提取关键短语](text-analytics-how-to-keyword-extraction.md)
