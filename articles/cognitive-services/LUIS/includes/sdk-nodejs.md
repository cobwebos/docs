---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-javascript, cog-serv-seo-aug-2020
ms.author: diberry
ms.openlocfilehash: b961bb0598a1f6e9cdc7b4df27af50e7af1d9d81
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323050"
---
使用适用于 Node.js 的语言理解 (LUIS) 客户端库来执行以下操作：

* 创建应用
* 使用示例言语添加意向（机器学习实体）
* 训练和发布应用
* 查询预测运行时

[参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) |  [创作](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring)和[预测](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime)库源代码 | [创作](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)和[预测](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) NPM | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org)
* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 有了 Azure 订阅后，在 Azure 门户中[创建语言理解创作资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)，以获取创作密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从[创建](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal)的资源获取密钥和终结点，以便将应用程序连接到语言理解创作。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。 可以使用免费定价层 (`F0`) 来试用该服务。

## <a name="setting-up"></a>设置

### <a name="create-a-new-javascript-application"></a>创建新的 JavaScript 应用程序

1. 在控制台窗口中，为应用程序创建一个新目录，并将其移动到该目录中。

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. 通过创建 `package.json` 文件，将该目录初始化为 JavaScript 应用程序。

    ```console
    npm init -y
    ```

1. 为 JavaScript 代码创建名为 `index.js` 的文件。

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>安装 NPM 库

在应用程序目录中，使用以下命令安装依赖项，每次执行一行：

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

`package.json` 应如下所示：

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>创作对象模型

语言理解 (LUIS) 创作客户端是对 Azure 进行身份验证的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) 对象，其中包含创作密钥。

## <a name="code-examples-for-authoring"></a>创作的代码示例

创建客户端后，可以使用此客户端访问如下所述的功能：

* 应用 - [添加](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-)、[删除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-)、[发布](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* 示例言语 - [按批添加](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-)、[按 ID 删除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* 特征 - 管理[短语列表](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* 模型 - 管理[意向](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-)和实体
* 模式 - 管理[模式](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* 训练 - [训练](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-)应用和轮询[训练状态](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [版本](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - 使用克隆、导出和删除操作进行管理

## <a name="prediction-object-model"></a>预测对象模型

语言理解 (LUIS) 创作客户端是对 Azure 进行身份验证的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 对象，其中包含创作密钥。

## <a name="code-examples-for-prediction-runtime"></a>预测运行时的代码示例

创建客户端后，可以使用此客户端访问如下所述的功能：

* 按 `staging` 或 `production` 槽进行[预测](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)
* [按版本进行预测](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>添加依赖项

在首选编辑器或 IDE 中打开 `index.js` 文件，并添加以下依赖项。

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>添加样板代码

1. 添加 `quickstart` 方法及其调用。 此方法保存大部分剩余代码。 在文件末尾调用此方法。

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. 在 quickstart 方法中添加剩余代码（除非另有指定）。

## <a name="create-variables-for-the-app"></a>为应用创建变量

创建两组变量：第一组为你更改的变量，第二组保留在代码示例中显示的状态。 

1. 创建用于保存创作密钥和资源名称的变量。

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. 创建用于保存终结点、应用名称、版本和意向名称的变量。

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>验证客户端

使用密钥创建 [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) 对象，并在终结点中使用该对象创建一个 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) 对象。

[!code-javascript[Authenticate the client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>创建 LUIS 应用

LUIS 应用包含自然语言处理 (NLP) 模型，包括意向、实体和示例言语。

创建 [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 对象的 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 方法，用于创建应用。 名称和语言区域性是必需的属性。

[!code-javascript[Create a LUIS app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>为应用创建意向
LUIS 应用模型中的主要对象是意向。 意向与用户言语意向的分组相符。 用户可以提问，或者做出表述，指出希望机器人（或其他客户端应用程序）提供特定的有针对性响应。 意向的示例包括预订航班、询问目的地城市的天气，以及询问客户服务的联系信息。

将 [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) 方法与唯一意向的名称配合使用，然后传递应用 ID、版本 ID 和新的意向名称。

`intentName` 值硬编码为 `OrderPizzaIntent`，作为[为应用创建变量](#create-variables-for-the-app)中变量的一部分。

[!code-javascript[Create intent for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>为应用创建实体

尽管实体不是必需的，但在大多数应用中都可以看到实体。 实体从用户言语中提取信息，只有使用这些信息才能实现用户的意向。 有多种类型的[预生成](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-)实体和自定义实体，每种实体具有自身的数据转换对象 (DTO) 模型。  在应用中添加的常见预生成实体包括 [number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md) 和 [ordinal](../luis-reference-prebuilt-ordinal.md)。

必须知道，实体不会使用意向进行标记。 它们可以并且通常应用到多个意向。 只会为特定的单个意向标记示例用户言语。

实体的创建方法属于 [Model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) 类的一部分。 每个实体类型有自身的数据转换对象 (DTO) 模型。

实体创建代码会创建机器学习实体，其中包含子实体以及应用于 `Quantity` 子实体的功能。

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="显示创建的实体的门户的部分屏幕截图，其中为包含子实体以及应用于 `Quantity` 子实体的功能的机器学习实体。":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

将以下方法置于 `quickstart` 方法之上，查找 Quantity 子实体的 ID，以便将功能分配给该子实体。

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>将示例言语添加到意向

为了确定言语的意向并提取实体，应用需要言语示例。 这些示例需要针对特定的单个意向，并且应该标记所有自定义实体。 无需标记预生成实体。

通过创建 [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) 对象的列表来添加示例言语（每个示例言语对应于一个对象）。 每个示例应使用实体名称和实体值的名称/值对字典来标记所有实体。 实体值应与示例言语文本中显示的值完全相同。

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="显示门户中标记的示例言语的部分屏幕截图。":::

结合应用 ID、版本 ID 和示例调用 [examples.add](https://docs.microsoft.com//javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#add-string--string--examplelabelobject--models-examplesaddoptionalparams-)。

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>训练应用

创建模型后，需要为此模型版本训练 LUIS 应用。 训练后的模型可在[容器](../luis-container-howto.md)中使用，或者将其[发布](../luis-how-to-publish-app.md)到过渡槽或生产槽。

[train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) 方法需要应用 ID 和版本 ID。

极小的模型（如本快速入门中所示的模型）很快就能完成训练。 对于生产级应用程序，应用的训练应该包括轮询调用 [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) 方法以确定训练何时成功或者是否成功。 响应是一个 [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) 对象列表，其中分别列出了每个对象的状态。 所有对象必须成功，才能将训练视为完成。

[!code-javascript[Train the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>将应用发布到生产槽

使用 [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) 方法发布 LUIS 应用。 这会将当前已训练的版本发布到终结点上的指定槽。 客户端应用程序使用此终结点发送用户言语，以预测意向和提取实体。

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>对预测运行时客户端进行身份验证

将 msRest.ApiKeyCredentials 对象与密钥一起使用，并在终结点中使用该对象创建一个 [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 对象。

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>从运行时获取预测

添加以下代码以创建对预测运行时的请求。 用户言语是 [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) 对象的一部分。

**[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** 方法需要多个参数，如应用 ID、槽名称、用于满足请求的预测请求对象。 其他选项（如详细、显示所有意向和日志）都是可选的。

[!code-javascript [Get prediction from runtime](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node index.js` 命令运行应用程序。

```console
node index.js
```
