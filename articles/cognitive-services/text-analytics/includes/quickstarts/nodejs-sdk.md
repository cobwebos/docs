---
title: 快速入门：适用于 Node.js 的文本分析 v3 客户端库 | Microsoft Docs
description: 适用于 Node.js 的 v3 版文本分析客户端库入门。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: ee1e3cbade33f1786e8b93a549d19b87eba189e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203387"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

[v3 参考文档](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3 库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 包(NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

[v2 参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 库源代码](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2 包(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org/)。
* 你有了 Azure 订阅后，<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="创建文本分析资源"  target="_blank">将在 Azure 门户中创建文本分析资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，以获取你的密钥和终结点。 
    * 你需要从创建的资源获取密钥和终结点，以便将应用程序连接到文本分析 API。 稍后会在本快速入门中执行此操作。
    * 可以使用免费定价层试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

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

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

安装 `@azure/ai-text-analytics` NPM 包：

```console
npm install --save @azure/ai-text-analytics
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以[在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)找到它，其中包含此快速入门中的代码示例。 

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

安装 `@azure/cognitiveservices-textanalytics` NPM 包：

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以[在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js)找到它，其中包含此快速入门中的代码示例。 

---

应用的 `package.json` 文件将使用依赖项进行更新。
创建一个名为 `index.js` 的文件，并添加以下内容：

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

为资源的 Azure 终结点和密钥创建变量。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

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
* [情绪分析](#sentiment-analysis) 
* [语言检测](#language-detection)
* [命名实体识别](#named-entity-recognition-ner)
* [实体链接](#entity-linking)
* [关键短语提取](#key-phrase-extraction)

## <a name="client-authentication"></a>客户端身份验证

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

创建一个新的 `TextAnalyticsClient` 对象并使用你的密钥和终结点作为参数。

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

使用 `credentials` 和 `endpoint` 作为参数创建新的 [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) 对象。

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>情绪分析

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `analyzeSentiment()` 方法，并获取返回的 `SentimentBatchResult` 对象。 循环访问结果列表，输出每个文档的 ID、文档级别情绪以及置信度分数。 对于每个文档，结果都包含句子级别情绪以及偏移量、长度和置信度分数。

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
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
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

创建一个字典对象列表，用以包含你要分析的文档。 调用客户端的 [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) 方法，并获取返回的 [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult)。 循环访问结果列表，输出每个文档的 ID 和情绪分数。 评分接近 0 表示消极情绪，评分接近 1 表示积极情绪。

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>语言检测

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `detectLanguage()` 方法，并获取返回的 `DetectLanguageResultCollection`。 然后循环访问结果，输出每个文档的 ID 以及各自的主要语言。

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

创建包含你的文档的字典对象的列表。 调用客户端的 [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) 方法，并获取返回的 [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)。 然后循环访问结果，输出每个文档的 ID 和语言。

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

> [!NOTE]
> 在版本 `3.0-preview` 中：
> * NER 包含单独用于检测个人信息的方法。 
> * 实体链接是一个独立于 NER 的请求。

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
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.31
```

## <a name="using-ner-to-detect-personal-information"></a>使用 NER 检测个人信息

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizePiiEntities()` 方法，并获取 `EntitiesBatchResult` 对象。 循环访问结果列表，并输出实体名称、类型、子类型、偏移量、长度和分数。


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ];
    const entityPiiResults = await client.recognizePiiEntities(entityPiiInput);

    entityPiiResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Score: 0.85
```

## <a name="entity-linking"></a>实体链接

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizeLinkedEntities()` 方法，并获取 `RecognizeLinkedEntitiesResult` 对象。 循环访问结果列表，并输出实体名称、ID、数据源、URL 和匹配项。 `matches` 数组中的每个对象都将包含该匹配项的偏移量、长度和分数。

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.score.toFixed(2)}`);
            });
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
                Text: Altair 8800       Score: 0.78
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.55
                Text: Gates     Score: 0.55
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.53
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.47
                Text: Microsoft         Score: 0.47
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.25
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.28
```

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

> [!NOTE]
> 在版本 2.1 中，实体链接包含在 NER 响应中。

创建对象的列表，其中包含你的文档。 调用客户端的 [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) 方法，并获取 [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) 对象。 循环访问结果列表，输出每个文档的 ID。 对于每个检测到的实体，输出其维基百科名称、类型和子类型（如果存在），以及其在原始文本中的位置。

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>关键短语提取

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `extractKeyPhrases()` 方法，并获取返回的 `ExtractKeyPhrasesResult` 对象。 循环访问结果，输出每个文档的 ID 以及任何检测到的密钥短语。

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
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

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

创建对象的列表，其中包含你的文档。 调用客户端的 [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) 方法，并获取返回的 [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) 对象。 循环访问结果，输出每个文档的 ID 以及任何检测到的密钥短语。

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```
