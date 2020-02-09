---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: e4bba00d6506684a3baf345806bdaa340f6970ca
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909166"
---
使用适用于 Node.js 的语言理解 (LUIS) 创作客户端库可以：

* 创建应用。
* 添加意向、实体和示例话语。
* 添加短语列表等特征。
* 训练和发布应用。
* 删除应用

[参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [创作包 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)、[运行时包 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>必备条件

* 语言理解创作资源：[在 Azure 门户中创建一个](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>设置

### <a name="get-your-language-understanding-luis-starter-key"></a>获取语言理解 (LUIS) 初学者密钥

通过创建 LUIS 创作资源，获取[初学者密钥](../luis-how-to-azure-subscription.md#starter-key)。 保留密钥和密钥的终结点，以便用于下一步。

### <a name="create-an-environment-variable"></a>创建环境变量

使用密钥和密钥区域，创建两个用于身份验证的环境变量：

* `LUIS_AUTHORING_KEY` - 用于验证请求的资源密钥。
* `LUIS_AUTHORING_ENDPOINT` - 与你的密钥关联的终结点。

使用操作系统的说明。

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

添加环境变量后，请重启控制台窗口。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

编辑 `.bash_profile`，然后添加环境变量：

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source .bash_profile`，使更改生效。
***

### <a name="install-the-npm-library-for-luis-authoring"></a>安装适用于 LUIS 创作的 NPM 库

在应用程序目录中，使用以下命令安装依赖项：

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>对象模型

语言理解 (LUIS) 创作客户端是对 Azure 进行身份验证的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) 对象，其中包含创作密钥。

创建客户端后，可以使用此客户端访问如下所述的功能：

* 应用 - [添加](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-)、[删除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-)、[发布](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* 示例言语 - [按批添加](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-)、[按 ID 删除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* 特征 - 管理[短语列表](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* 模型 - 管理[意向](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-)和实体
* 模式 - 管理[模式](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* 训练 - [训练](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-)应用和轮询[训练状态](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [版本](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - 使用克隆、导出和删除操作进行管理


## <a name="code-examples"></a>代码示例

这些代码片段展示了如何使用适用于 Node.js 的语言理解 (LUIS) 创作客户端库来执行以下操作：

* [创建应用](#create-a-luis-app)
* [添加实体](#create-entities-for-the-app)
* [添加意向](#create-intent-for-the-app)
* [添加示例表述](#add-example-utterance-to-intent)
* [训练应用](#train-the-app)
* [发布应用](#publish-a-language-understanding-app)
* [删除应用](#delete-a-language-understanding-app)
* [列出应用](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在你喜欢使用的编辑器或 IDE 中创建名为 `luis_authoring_quickstart.js` 的新文本文件。 然后，添加以下依赖项。

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

为资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>验证客户端

使用密钥创建 [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) 对象，并在终结点中使用该对象创建一个 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) 对象。

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>创建 LUIS 应用

1. 创建一个 LUIS 应用，用于包含保存意向、实体和示例言语的自然语言处理 (NLP) 模型。

1. 创建 [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 对象的 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 方法，用于创建应用。 名称和语言区域性是必需的属性。

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>为应用创建意向
LUIS 应用模型中的主要对象是意向。 意向与用户言语意向的分组相符。  用户可以提问，或者做出表述，指出希望机器人（或其他客户端应用程序）提供特定的有针对性响应。  意向的示例包括预订航班、询问目的地城市的天气，以及询问客户服务的联系信息。

将 [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) 方法与唯一意向的名称配合使用，然后传递应用 ID、版本 ID 和新的意向名称。

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>为应用创建实体

尽管实体不是必需的，但在大多数应用中都可以看到实体。 实体从用户言语中提取信息，只有使用这些信息才能实现用户的意向。 有多种类型的[预生成](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-)实体和自定义实体，每种实体具有自身的数据转换对象 (DTO) 模型。  在应用中添加的常见预生成实体包括 [number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md) 和 [ordinal](../luis-reference-prebuilt-ordinal.md)。

此 **add_entities** 方法创建一个包含两个角色的 `Location` 简单实体、一个 `Class` 简单实体和一个 `Flight` 复合实体，并添加多个预生成实体。

必须知道，实体不会使用意向进行标记。 它们可以并且通常应用到多个意向。 只会为特定的单个意向标记示例用户言语。

实体的创建方法属于 [Model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) 类的一部分。 每个实体类型有自身的数据转换对象 (DTO) 模型。

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>将示例言语添加到意向

为了确定言语的意向并提取实体，应用需要言语示例。 这些示例需要针对特定的单个意向，并且应该标记所有自定义实体。 无需标记预生成实体。

通过创建 [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) 对象的列表来添加示例言语（每个示例言语对应于一个对象）。 每个示例应使用实体名称和实体值的名称/值对字典来标记所有实体。 实体值应与示例言语文本中显示的值完全相同。

结合应用 ID、版本 ID 和示例列表调用 [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-)。 该调用将以结果列表做出响应。 需要检查每个示例的结果，以确保该示例已成功添加到模型中。

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>训练应用

创建模型后，需要为此模型版本训练 LUIS 应用。 训练后的模型可在[容器](../luis-container-howto.md)中使用，或者将其[发布](../luis-how-to-publish-app.md)到过渡槽或生产槽。

[train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) 方法需要应用 ID 和版本 ID。

极小的模型（如本快速入门中所示的模型）很快就能完成训练。 对于生产级应用程序，应用的训练应该包括轮询调用 [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) 方法以确定训练何时成功或者是否成功。 响应是一个 [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) 对象列表，其中分别列出了每个对象的状态。 所有对象必须成功，才能将训练视为完成。

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

训练所有模型会花费一些时间。 使用 operationResult 检查训练状态。

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>发布语言理解应用

使用 [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) 方法发布 LUIS 应用。 这会将当前已训练的版本发布到终结点上的指定槽。 客户端应用程序使用此终结点发送用户言语，以预测意向和提取实体。

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>删除语言理解应用

使用 [app.deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) 方法删除 LUIS 应用。 这将删除当前应用。

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>列出语言理解应用

列出与语言理解密钥关联的应用列表

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node luis_authoring_quickstart.js` 命令运行应用程序。

```console
node luis_authoring_quickstart.js
```

应用程序的命令行输出为：

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
