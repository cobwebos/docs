---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 9b148e413bc7dc6af7eff064e5ff3ec6385cfef4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750130"
---
[参考文档](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [包 (Github)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Go](https://golang.org/dl/)

## <a name="setting-up"></a>设置

### <a name="create-a-text-analytics-azure-resource"></a>创建文本分析 Azure 资源 

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-go-project"></a>创建新的 Go 项目

在控制台窗口（cmd、PowerShell、终端、Bash）中，为 Go 项目创建一个新的工作区并导航到该工作区。 工作区包含三个文件夹： 

* **src** - 此目录包含源代码和包。 使用 `go get` 命令安装的任何包将位于此处。
* **pkg** - 此目录包含编译的 Go 包对象。 这些文件使用 `.a` 扩展名。
* **bin** - 此目录包含运行 `go install` 时创建的二进制可执行文件。

> [!TIP]
> 详细了解 [Go 工作区](https://golang.org/doc/code.html#Workspaces)的结构。 本指南包含有关设置 `$GOPATH` 和 `$GOROOT` 的信息。

创建名为 `my-app` 的工作区，并为 `src`、`pkg` 和 `bin` 创建所需的子目录：

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>安装适用于 Go 的文本分析客户端库

安装适用于 Go 的客户端库： 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

或者，如果使用 dep，则在存储库中运行：

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>创建 Go 应用程序

接下来，创建名为 `src/quickstart.go` 的文件：

```bash
$ cd src
$ touch quickstart.go
```

在喜好的 IDE 或文本编辑器中打开 `quickstart.go`。 然后添加包名称并导入以下库：

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>对象模型 

文本分析客户端是一个 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) 对象，它使用你的密钥向 Azure 进行身份验证。 该客户端提供了几种方法来分析文本，文本可以是单个字符串，也可以是批处理。 

文本将以 `documents` 的列表的形式发送到 API，该项是包含 `id`、`text` 和 `language` 属性的组合的 `dictionary` 对象，具体取决于所用的方法。 `text` 属性存储要以源 `language` 分析的文本，而 `id` 则可以是任何值。 

响应对象是一个列表，其中包含每个文档的分析信息。 

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的文本分析客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [情绪分析](#sentiment-analysis)
* [语言检测](#language-detection)
* [实体识别](#entity-recognition)
* [关键短语提取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>验证客户端


在新函数中，为资源的 Azure 终结点和订阅密钥创建变量。 从环境变量 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 和 `TEXT_ANALYTICS_ENDPOINT` 获取这些值。 如果在开始编辑应用程序后创建了这些环境变量，则需要关闭并重新打开用于访问这些变量的编辑器、IDE 或 shell。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

创建新的 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) 对象。 将密钥传递给 [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) 函数，随后它将传递给客户端的 `authorizer` 属性。

[!code-go[Client creation ](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=client)]

## <a name="sentiment-analysis"></a>情绪分析

创建名为 `SentimentAnalysis()` 的新函数，并使用先前创建的 `GetTextAnalyticsClient()` 方法创建客户端。 创建 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 对象的列表，其中包含要分析的文档。 每个对象会包含 `id`、`Language` 和 `text` 属性。 `text` 属性存储要分析的文本，`language` 是文档的语言，`id` 则可以是任何值。 

调用客户端的 [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) 函数并获取结果。 然后循环访问结果，输出每个文档的 ID 和情绪分数。 评分接近 0 表示消极情绪，评分接近 1 表示积极情绪。

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

在项目中调用 `SentimentAnalysis()`。

### <a name="output"></a>输出

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>语言检测

创建名为 `LanguageDetection()` 的新函数，并使用先前创建的 `GetTextAnalyticsClient()` 方法创建客户端。 创建 [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) 对象的列表，其中包含要分析的文档。 每个对象会包含 `id` 和 `text` 属性。 `text` 属性存储要分析的文本，而 `id` 则可以是任何值。 

调用客户端的 [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) 并获取结果。 然后循环访问结果，输出每个文档的 ID 和检测到的语言。

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

在项目中调用 `LanguageDetection()`。

### <a name="output"></a>输出

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>实体识别

创建名为 `ExtractEntities()` 的新函数，并使用先前创建的 `GetTextAnalyticsClient()` 方法创建客户端。 创建 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 对象的列表，其中包含要分析的文档。 每个对象会包含 `id`、`language` 和 `text` 属性。 `text` 属性存储要分析的文本，`language` 是文档的语言，`id` 则可以是任何值。 

调用客户端的 [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) 并获取结果。 然后循环访问结果，输出每个文档的 ID 和提取的实体分数。

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

在项目中调用 `ExtractEntities()`。

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

创建名为 `ExtractKeyPhrases()` 的新函数，并使用先前创建的 `GetTextAnalyticsClient()` 方法创建客户端。 创建 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 对象的列表，其中包含要分析的文档。 每个对象会包含 `id`、`language` 和 `text` 属性。 `text` 属性存储要分析的文本，`language` 是文档的语言，`id` 则可以是任何值。

调用客户端的 [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) 并获取结果。 然后循环访问结果，输出每个文档的 ID 以及提取的关键短语。

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

在项目中调用 `ExtractKeyPhrases()`。

### <a name="output"></a>输出

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
