---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 518e6d544547b808b278121bf6364dcd1590bd6f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281122"
---
<a name="HOLTop"></a>

[参考文档](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [包 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * 本快速入门使用文本分析客户端库的 `3.0-preview` 版本，这包括改进的[情绪分析](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)和[命名实体识别 (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) 的公共预览版。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 例如，从 `azure.ai.textanalytics.aio` 命名空间导入客户端并调用 `analyze_sentiment()`，而非从 `azure.ai.textanalytics` 命名空间调用 `analyze_sentiment()`。

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>设置

### <a name="create-a-text-analytics-azure-resource"></a>创建文本分析 Azure 资源

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>安装客户端库

在安装 Python 后，可以通过以下命令安装客户端库：

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

创建一个新的 Python 文件，为资源的 Azure 终结点和订阅密钥创建变量。

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>对象模型

文本分析客户端是一个 `TextAnalyticsClient` 对象，它使用你的密钥向 Azure 进行身份验证。 该客户端提供了几种方法来成批分析文本。 本快速入门使用一系列函数来快速发送单个文档。

当成批处理时，文本将以 `documents` 的列表的形式发送到 API，该项是包含 `id`、`text` 和 `language` 属性的组合的 `dictionary` 对象，具体取决于所用的方法。 `text` 属性存储要以源 `language` 分析的文本，而 `id` 则可以是任何值。 处理单个文档时，只需要 `text` 输入，如以下示例中所示。  

响应对象是一个列表，其中包含每个文档的分析信息。 

## <a name="code-examples"></a>代码示例

这些代码片段展示了如何使用适用于 Python 的文本分析客户端库执行以下任务：

* [情绪分析](#sentiment-analysis)（公共预览版）
* [语言检测](#language-detection)
* [命名实体识别](#named-entity-recognition-public-preview)（公共预览版）
* [命名实体识别 - 个人信息](#named-entity-recognition---personal-information-public-preview)（公共预览版）
* [实体链接](#entity-linking)
* [关键短语提取](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>情绪分析

> [!NOTE]
> 以下代码适用于情绪分析 v3（当前为公共预览版）。

创建一个名为 `sentiment_analysis_example()` 的新函数，该函数采用终结点和密钥作为参数，然后调用 `single_analyze_sentiment()` 函数。 返回的响应对象将包含整个输入文档的情绪标签和分数，以及每个句子的情绪分析。


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, key=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>输出

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>语言检测

创建一个名为 `language_detection_example()` 的新函数，该函数采用终结点和密钥作为参数，然后调用 `single_detect_languages()` 函数。 如果成功，则返回的响应对象将在 `detected_languages` 中包含检测到的语言，否则将包含 `error`。

> [!Tip]
> 在某些情况下，可能很难根据输入区分语言。 可以使用 `country_hint` 参数指定 2 个字母的国家/地区代码。 默认情况下，API 使用“US”作为默认的 countryHint，要删除此行为，可以通过将此值设置为空字符串 `country_hint : ""` 来重置此参数。 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, key=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>输出

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>命名实体识别（公共预览版）

> [!NOTE]
> 以下代码适用于命名实体识别 v3（当前为公共预览版）。

创建一个名为 `entity_recognition_example` 的新函数，该函数采用终结点和密钥作为参数，然后调用 `single_recognize_entities()` 函数并循环访问结果。 如果成功，则返回的响应对象将在 `entity` 中包含检测到的实体列表，否则将包含 `error`。 对于检测到的每个实体，输出其类型和子类型（如果存在）。

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>输出

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>命名实体识别 - 个人信息（公共预览版）

> [!NOTE]
> 下面的代码使用命名实体识别 v3（当前为公共预览版）来检测个人信息。

创建一个名为 `entity_pii_example()` 的新函数，该函数采用终结点和密钥作为参数，然后调用 `single_recognize_pii_entities()` 函数并获取结果。 然后，循环访问结果并输出实体。

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>输出

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>实体链接

创建一个名为 `entity_linking_example()` 的新函数，该函数采用终结点和密钥作为参数，然后调用 `single_recognize_linked_entities()` 函数并循环访问结果。 如果成功，则返回的响应对象将在 `entities` 中包含检测到的实体列表，否则将包含 `error`。 由于链接实体是唯一标识的，因此同一实体的实例将以分组形式出现在 `entity` 对象下，显示为 `match` 对象的列表。

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, key=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>输出

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>关键短语提取

创建一个名为 `key_phrase_extraction_example()` 的新函数，该函数采用终结点和密钥作为参数，然后调用 `single_extract_key_phrases()` 函数。 如果成功，结果将包含 `key_phrases` 中检测到的关键短语列表，如果失败，则将包含 `error`。 输出任何检测到的关键短语。

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, key=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>输出

```console
    Key Phrases:
         cat
         veterinarian
```