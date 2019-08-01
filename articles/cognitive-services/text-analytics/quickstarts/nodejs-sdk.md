---
title: 快速入门：使用 Node.js 调用文本分析 API
titleSuffix: Azure Cognitive Services
description: 获取信息和代码示例，以帮助你快速开始使用文本分析 API。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697478"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>快速入门：使用 Node.js 调用文本分析认知服务
<a name="HOLTop"></a>

根据本快速入门中的说明，开始使用用于 Node.js 的文本分析 SDK 来分析语言。 虽然[文本分析](//go.microsoft.com/fwlink/?LinkID=759711) REST API 与大多数编程语言兼容，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js) 上找到此示例的源代码。

有关 API 的技术文档，请参阅 [API 定义](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org/)
* [用于 Node.js 的文本分析 SDK](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) 可以通过以下命令来安装此 SDK：

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

还必须拥有在注册期间生成的[终结点和访问密钥](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>创建 Node.js 应用程序并安装 SDK

安装 Node.js 后，创建一个 Node 项目。 为应用创建一个新目录，然后导航到其目录。

```mkdir myapp && cd myapp```

运行 ```npm init``` 以使用 package.json 文件创建一个 node 应用程序。 安装 `ms-rest-azure` 和 `azure-cognitiveservices-textanalytics` NPM 包:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

应用的 package.json 文件将随依赖项进行更新。

## <a name="authenticate-your-credentials"></a>对凭据进行验证

在项目根目录中创建新文件 `index.js` 并导入已安装的库

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

为文本分析订阅密钥创建变量。

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> 若要在生产系统中安全地部署机密，建议你使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)。
>

## <a name="create-a-text-analytics-client"></a>创建文本分析客户端

使用 `credentials` 作为参数创建新的 `TextAnalyticsClient` 对象。 请使用适合你的文本分析订阅的正确 Azure 区域。

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>情绪分析

创建对象的列表，其中包含要分析的文档。 API 的有效负载由 `documents` 列表组成，而列表中的项包含 `id`、`language` 和 `text` 属性。 `text` 属性存储要分析的文本，`language` 是文档的语言，`id` 则可以是任何值。 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

调用 `client.sentiment` 并获取结果。 然后循环访问结果，输出每个文档的 ID 和情绪分数。 评分接近 0 表示消极情绪，评分接近 1 表示积极情绪。

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>语言检测

创建对象的列表，其中包含你的文档。 API 的有效负载由 `documents` 列表组成，而列表中的项包含 `id` 和 `text` 属性。 `text` 属性存储要分析的文本，而 `id` 则可以是任何值。

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

调用 `client.detectLanguage()` 并获取结果。 然后循环访问结果，输出每个文档的 ID 和第一种返回的语言。

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>实体识别

创建对象的列表，其中包含你的文档。 API 的有效负载由 `documents` 列表组成，而列表中的项包含 `id`、`language` 和 `text` 属性。 `text` 属性存储要分析的文本，`language` 是文档的语言，`id` 则可以是任何值。

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

调用 `client.entities()` 并获取结果。 然后循环访问结果，输出每个文档的 ID。 对于每个检测到的实体，输出其维基百科名称、类型和子类型（如果存在），以及其在原始文本中的位置。

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>关键短语提取

创建对象的列表，其中包含你的文档。 API 的有效负载由 `documents` 列表组成，而列表中的项包含 `id`、`language` 和 `text` 属性。 `text` 属性存储要分析的文本，`language` 是文档的语言，`id` 则可以是任何值。

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

调用 `client.keyPhrases()` 并获取结果。 然后循环访问结果，输出每个文档的 ID 以及任何检测到的关键短语。

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 进行文本分析](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另请参阅

 [文本分析概述](../overview.md)[常见问题解答 (FAQ)](../text-analytics-resource-faq.md)
