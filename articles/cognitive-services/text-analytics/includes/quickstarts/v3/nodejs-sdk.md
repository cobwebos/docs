---
title: 快速入门：适用于 Node.js 的文本分析 v3 客户端库 | Microsoft Docs
description: 适用于 Node.js 的 v3 版文本分析客户端库入门。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 69899f521e73cb5af1af145a0915dbe1a017f307
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281124"
---
<a name="HOLTop"></a>

[参考文档](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [包 (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)

> [!NOTE]
> * 本快速入门使用文本分析客户端库的 `3.0-preview` 版本，这包括改进的[情绪分析](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)和[命名实体识别 (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) 的公共预览版。
> * 为了简单起见，本文中的代码使用了不受保护的凭据存储。 对于生产方案，我们建议成批发送字符串以提高性能和可伸缩性。 例如，调用 `SentimentBatchAsync()` 而非 `Sentiment()`。

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org/)。

## <a name="setting-up"></a>设置

### <a name="create-a-text-analytics-azure-resource"></a>创建文本分析 Azure 资源

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```
### <a name="install-the-client-library"></a>安装客户端库

安装 `@azure/cognitiveservices-textanalytics` NPM 包：

```console
npm install --save @azure/cognitiveservices-textanalytics
```

应用的 `package.json` 文件将使用依赖项进行更新。

创建一个名为 `index.js` 的文件，并添加以下库：

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

为资源的 Azure 终结点和密钥创建变量。

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>对象模型

文本分析客户端是一个 `TextAnalyticsClient` 对象，它使用你的密钥向 Azure 进行身份验证。 该客户端提供了几种方法来分析文本，文本可以是单个字符串，也可以是批处理。

文本将以 `documents` 的列表的形式发送到 API，该项是包含 `id`、`text` 和 `language` 属性的组合的 `dictionary` 对象，具体取决于所用的方法。 `text` 属性存储要以源 `language` 分析的文本，而 `id` 则可以是任何值。 

响应对象是一个列表，其中包含每个文档的分析信息。 

## <a name="code-examples"></a>代码示例

* [客户端身份验证](#client-authentication)
* [情绪分析](#sentiment-analysis)（公共预览版）
* [语言检测](#language-detection)
* [命名实体识别](#named-entity-recognition-public-preview)（公共预览版）
* [命名实体识别 - 个人信息](#named-entity-recognition---personal-information-public-preview)（公共预览版）
* [实体链接](#entity-linking)
* [关键短语提取](#key-phrase-extraction)

## <a name="client-authentication"></a>客户端身份验证

创建一个新的 `TextAnalyticsClient` 对象并使用你的密钥和终结点作为参数。

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

## <a name="sentiment-analysis-public-preview"></a>情绪分析（公共预览版）

> [!NOTE]
> 以下代码适用于情绪分析 v3（当前为公共预览版）。

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `analyzeSentiment()` 方法，并获取返回的 `SentimentBatchResult` 对象。 循环访问结果列表，输出每个文档的 ID、文档级别情绪以及置信度分数。 对于每个文档，结果都包含句子级别情绪以及偏移量、长度和置信度分数。

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

## <a name="language-detection"></a>语言检测

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `detectLanguages()` 方法，并获取返回的 `DetectLanguageResult`。 然后循环访问结果，输出每个文档的 ID 以及各自的主要语言和检测到的语言。

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Detected Language French
        Primary Language French
```

## <a name="named-entity-recognition"></a>命名实体识别

> [!NOTE]
> 以下代码适用于命名实体识别 v3（当前为公共预览版）。

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizeEntities()` 方法，并获取 `RecognizeEntitiesResult` 对象。 循环访问结果列表，并输出实体名称、类型、子类型、偏移量、长度和分数。

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];

    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 0
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>命名实体识别 - 个人信息（公共预览版）

> [!NOTE]
> 下面的代码使用命名实体识别 v3（当前为公共预览版）来检测个人信息。

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizePiiEntities()` 方法，并获取 `EntitiesBatchResult` 对象。 循环访问结果列表，并输出实体名称、类型、子类型、偏移量、长度和分数。


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 0
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>实体链接

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizeLinkedEntities()` 方法，并获取 `RecognizeLinkedEntitiesResult` 对象。 循环访问结果列表，并输出实体名称、ID、数据源、URL 和匹配项。 `matches` 数组中的每个对象都将包含该匹配项的偏移量、长度和分数。

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

## <a name="key-phrase-extraction"></a>关键短语提取

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `extractKeyPhrases()` 方法，并获取返回的 `ExtractKeyPhrasesResult` 对象。 循环访问结果，输出每个文档的 ID 以及任何检测到的密钥短语。

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```
