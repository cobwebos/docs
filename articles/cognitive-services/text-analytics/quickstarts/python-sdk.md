---
title: 快速入门：使用 Python SDK 调用文本分析服务
titleSuffix: Azure Cognitive Services
description: 获取信息和代码示例，以便快速完成 Azure 认知服务中的文本分析 API 的使用入门。
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 82f0313a237358fcaa1ae52e92821abef2b52af7
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697320"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>快速入门：使用 Python SDK 调用文本分析服务 
<a name="HOLTop"></a>

根据本快速入门中的说明，开始使用用于 Python 的文本分析 SDK 来分析语言。 虽然文本分析 REST API 与大多数编程语言兼容，但该 SDK 提供了一种简单方法来将服务集成到应用程序中，不需要对 JSON 进行序列化和反序列化。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py) 上找到此示例的源代码。

## <a name="prerequisites"></a>先决条件

* [Python 3.x](https://www.python.org/)

* [用于 python 的文本分析 SDK](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) 可以通过以下命令来安装此程序包：

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

在你喜欢使用的编辑器或 IDE 中创建一个新的 Python 应用程序。 然后，将以下 import 语句添加到你的文件。

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>对凭据进行验证

> [!Tip]
> 若要在生产系统中安全地部署机密，建议你使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)。
>

在为你的文本分析订阅密钥创建变量后，使用它来实例化一个 `CognitiveServicesCredentials` 对象。

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>创建文本分析客户端

使用 `credentials` 和 `text_analytics_url` 作为参数创建新的 `TextAnalyticsClient` 对象。 请使用适合你的文本分析订阅的正确 Azure 区域（例如 `westcentralus`）。

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>情绪分析

API 的有效负载由 `documents` 列表组成，而列表中的项是包含 `id` 和 `text` 属性的字典。 `text` 属性存储要分析的文本，而 `id` 则可以是任何值。 

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "I had the best day of my life."
    },
    {
        "id": "2",
        "language": "en",
        "text": "This was a waste of my time. The speaker put me to sleep."
    },
    {
        "id": "3",
        "language": "es",
        "text": "No tengo dinero ni nada que dar..."
    },
    {
        "id": "4",
        "language": "it",
        "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    }
]
```

调用 `sentiment()` 函数并获取结果。 然后循环访问结果，输出每个文档的 ID 和情绪分数。 评分接近 0 表示消极情绪，评分接近 1 表示积极情绪。

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>输出

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>语言检测

创建一个字典列表，每个字典都包含你要分析的文档。 `text` 属性存储要分析的文本，而 `id` 则可以是任何值。 

```python
documents = [
    {
        'id': '1',
        'text': 'This is a document written in English.'
    },
    {
        'id': '2',
        'text': 'Este es un document escrito en Español.'
    },
    {
        'id': '3',
        'text': '这是一个用中文写的文件'
    }
]
```

使用前面创建的客户端，调用 `detect_language()` 并获取结果。 然后循环访问结果，输出每个文档的 ID 和第一种返回的语言。

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>输出

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>实体识别

创建一个字典列表，用以包含你要分析的文档。 `text` 属性存储要分析的文本，而 `id` 则可以是任何值。 


```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es",
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

使用前面创建的客户端，调用 `entities()` 函数并获取结果。 然后循环访问结果，输出每个文档的 ID 和其中包含的实体。

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ", entity.name, "\tType: ",
              entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>输出

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>关键短语提取

创建一个字典列表，用以包含你要分析的文档。 `text` 属性存储要分析的文本，而 `id` 则可以是任何值。 


```python
documents = [
    {
        "id": "1",
        "language": "ja",
        "text": "猫は幸せ"
    },
    {
        "id": "2",
        "language": "de",
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3",
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4",
        "language": "es",
        "text": "A mi me encanta el fútbol!"
    }
]
```

使用前面创建的客户端，调用 `key_phrases()` 函数并获取结果。 然后循环访问结果，输出每个文档的 ID 和其中包含的关键短语。

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```

### <a name="output"></a>输出

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 进行文本分析](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另请参阅

* [什么是文本分析 API？](../overview.md)
* [示例用户方案](../text-analytics-user-scenarios.md)
* [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)
