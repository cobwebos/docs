---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 54aadd22b8f2bbccaa4c5e4f1444aa08279a4773
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125412"
---
<a name="HOLTop"></a>

[参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [库源代码](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [包 (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org/)。

## <a name="setting-up"></a>设置

### <a name="create-a-text-analytics-azure-resource"></a>创建文本分析 Azure 资源

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```

创建一个名为 `index.js` 的文件，并添加以下库：

[!code-javascript[Const statements](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=constStatements)]

为资源的 Azure 终结点和订阅密钥创建变量。 从环境变量 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 和 `TEXT_ANALYTICS_ENDPOINT` 获取这些值。 如果在开始编辑应用程序后创建了这些环境变量，则需要关闭并重新打开用于访问这些变量的编辑器、IDE 或 shell。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-javascript[Key and endpoint vars](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyVars)]

### <a name="install-the-client-library"></a>安装客户端库

安装 `@azure/ms-rest-js` 和 `@azure/cognitiveservices-textanalytics` NPM 包:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

应用的 `package.json` 文件将使用依赖项进行更新。

## <a name="object-model"></a>对象模型

文本分析客户端是一个 [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) 对象，它使用你的密钥向 Azure 进行身份验证。 该客户端提供了几种方法来分析文本，文本可以是单个字符串，也可以是批处理。

文本将以 `documents` 的列表的形式发送到 API，该项是包含 `id`、`text` 和 `language` 属性的组合的 `dictionary` 对象，具体取决于所用的方法。 `text` 属性存储要以源 `language` 分析的文本，而 `id` 则可以是任何值。 

响应对象是一个列表，其中包含每个文档的分析信息。 

## <a name="code-examples"></a>代码示例

* [对客户端进行身份验证](#authenticate-the-client)
* [情绪分析](#sentiment-analysis)
* [语言检测](#language-detection)
* [实体识别](#entity-recognition)
* [关键短语提取](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>验证客户端

使用 `credentials` 和 `endpoint` 作为参数创建新的 [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) 对象。

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]


## <a name="sentiment-analysis"></a>情绪分析

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

## <a name="language-detection"></a>语言检测

创建包含你的文档的字典对象的列表。 调用客户端的 [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) 方法，并获取返回的 [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)。 然后循环访问结果，输出每个文档的 ID 和语言。

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>实体识别

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

## <a name="key-phrase-extraction"></a>关键短语提取

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

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```
