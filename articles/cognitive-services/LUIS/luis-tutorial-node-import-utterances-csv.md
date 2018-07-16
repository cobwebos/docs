---
title: 使用 Node.js 以编程方式生成 LUIS 应用 | Microsoft Docs
titleSuffix: Azure
description: 了解如何使用 LUIS Authoring API 以编程方式从 CSV 格式的预先存在数据生成 LUIS 应用。
services: cognitive-services
author: DeniseMak
manager: rstand
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: 09c9d4da835b7b30fd132770f9d13b33fa80a3f5
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268308"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>使用 Node.js 以编程方式生成 LUIS 应用

LUIS 提供与 [LUIS][LUIS] 网站功能相同的编程 API。 如果有预先存在的数据，这样可以节省时间，而且以编程方式创建 LUIS 应用比手动输入信息快。 

## <a name="prerequisites"></a>先决条件

* 登录 [LUIS][LUIS] 网站，并在“帐户设置”中找到[创作密钥](luis-concept-keys.md#authoring-key)。 使用此密钥调用 Authoring API。
* 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 本教程从用户请求的一家虚拟公司的 CSV 格式日志文件开始。 可从[此处](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/IoT.csv)下载。
* 使用 NPM 安装最新的 Node.js。 从[此处](https://nodejs.org/en/download/)下载它。
* **[建议]** 用于 IntelliSense 和调试的 Visual Studio Code 可从[此处](https://code.visualstudio.com/)免费下载。

## <a name="map-preexisting-data-to-intents-and-entities"></a>将预先存在的数据映射到意向和实体
即使系统在创建时未考虑使用 LUIS，如果它包含映射到用户不同操作的文本数据，也许能够从现有用户输入类别映射到 LUIS 中的意向。 如果可标识用户所说的重要单词或短语，这些单词可能会映射到实体。

打开 `IoT.csv` 文件。 它包含对虚构家庭自动化服务的用户查询日志，包括分类方式、用户所说的内容以及一些包含有用信息的列。 

![CSV 文件](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

可以看到“RequestType”列可能是意向，“Request”列显示了一个示例陈述。 如果其他字段出现在陈述中，则可能是实体。 由于有意向、实体和示例陈述，因此需要一个简单的示例应用。

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>从非 LUIS 数据生成 LUIS 应用的步骤
若要从源文件生成新的 LUIS 应用，首先分析 CSV 文件中的数据并将此数据转换为可以使用 Authoring API 将其上传到 LUIS 的格式。 从已分析的数据，收集存在的意向和实体的信息。 然后进行 API 调用，以创建应用，并添加从已分析的数据收集的意向和实体。 创建 LUIS 应用后，可以从已分析的数据中添加示例陈述。 可以在下面的代码的最后一部分看到此流程。 复制或[下载](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/index.js)此代码并将其保存在 `index.js`。

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>分析 CSV

包含 CSV 中的陈述的列条目必须被分析为 LUIS 可以理解的 JSON 格式。 此 JSON 格式必须包含一个 `intentName` 字段，该字段标识陈述的意向。 它还必须包含一个 `entityLabels` 字段，如果陈述中没有实体，该字段可以是空的。 

例如，“打开灯”条目映射到此 JSON：

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

在此示例中，`intentName` 来自 CSV 文件中“Request”列标题下的用户请求，`entityName` 来自具有密钥信息的其他列。 例如，如果有“操作”或“设备”条目，并且该字符串也出现在实际请求中，则可将其标记为实体。 下面的代码演示此分析过程。 可以复制或[下载](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_parse.js)它并将其保存在 `_parse.js`。

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>创建 LUIS 应用
将数据分析到 JSON 后，将其添加到 LUIS 应用。 下面的代码创建 LUIS 应用。 复制或[下载](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_create.js)它，并将其保存到 `_create.js`。

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>添加意向
创建应用后需要向其添加意向。 下面的代码创建 LUIS 应用。 复制或[下载](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_intents.js)它，并将其保存到 `_intents.js`。

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>添加实体
以下代码向 LUIS 应用添加实体。 复制或[下载](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_entities.js)它，并将其保存到 `_entities.js`。

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>添加表达
LUIS 应用中定义了实体和意向后，可以添加陈述。 下面的代码使用 [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API，它允许每次添加最多 100 个陈述。  复制或[下载](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_upload.js)它，并将其保存到 `_upload.js`。

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>运行代码


### <a name="install-nodejs-dependencies"></a>安装 Node.js 依赖项
在终端/命令行中，从 NPM 安装 Node.js 依赖项。

````
> npm install
````

### <a name="change-configuration-settings"></a>更改配置设置
若要使用此应用程序，需要将 index.js 文件中的值更改为自己的订阅密钥，并提供希望应用拥有的名称。 还可以设置应用的区域性或更改版本号。

打开 index.js 文件，并在文件顶部更改这些值。


````JavaScript
// Change these values
const LUIS_programmaticKey = "YOUR_PROGRAMMATIC_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
````
### <a name="run-the-script"></a>运行脚本
使用 Node.js 从终端/命令行运行脚本。

````
> node index.js
````
或
````
> npm start
````

### <a name="application-progress"></a>应用程序进度
应用程序运行时，命令行显示进度。 命令行输出包括 LUIS 的响应格式。

````
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
````




## <a name="open-the-luis-app"></a>打开 LUIS 应用
该脚本完成后，可以登录 [LUIS][LUIS]，查看“我的应用”下创建的 LUIS 应用。 应该能够看到在 TurnOn、TurnOff 和 None 意向下添加的陈述。

![TurnOn 意向](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [测试和训练 LUIS 网站中的应用](interactive-test.md)

## <a name="additional-resources"></a>其他资源

此示例应用程序使用以下 LUIS API：
- [创建应用](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [添加意向](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [添加实体](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [添加陈述](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

