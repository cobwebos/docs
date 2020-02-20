---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 05e668ff5b0ec19c5e380cf6bfee4b6e46900b2f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372048"
---
可以使用适用于 Node.js 的语言理解 (LUIS) 运行时客户端库执行以下操作：

* 按槽进行预测
* 按版本进行预测

[参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [运行时包 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>必备条件

* 语言理解运行时资源：[在 Azure 门户中创建一个](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>设置

### <a name="get-your-language-understanding-luis-runtime-key"></a>获取语言理解 (LUIS) 运行时密钥

通过创建 LUIS 运行时资源，获取[运行时密钥](../luis-how-to-azure-subscription.md)。 保留密钥和密钥的终结点，以便用于下一步。

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>创建新的 javascript (Node.js) 文件

在首选编辑器或 IDE 中创建名为 `luis_prediction.js` 的新 javascript 文件。

### <a name="install-the-npm-library-for-the-luis-runtime"></a>安装适用于 LUIS 运行时的 NPM 库

在应用程序目录中，使用以下命令安装依赖项：

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>对象模型

语言理解 (LUIS) 创作客户端是对 Azure 进行身份验证的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 对象，其中包含创作密钥。

创建客户端后，可以使用此客户端访问如下所述的功能：

* 按 `staging` 或 `production` 槽进行[预测](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)
* [按版本进行预测](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用语言理解 (LUIS) 预测运行时客户端库执行以下操作：

* [按槽进行预测](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>添加依赖项

在偏好的编辑器或 IDE 中打开项目目录中的 `luis_prediction.js` 文件。 添加以下依赖项：

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>验证客户端

1. 为自己所需的 LUIS 信息创建变量：

    添加用于管理从 `LUIS_RUNTIME_KEY` 环境变量提取的预测密钥的变量。 如果在启动应用程序后创建了环境变量，则需要关闭并重新加载运行它的编辑器、IDE 或 shell 以访问该变量。 稍后会创建这些方法。

    创建保留资源名称 `LUIS_RUNTIME_ENDPOINT` 的变量。

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. 创建应用 ID 的变量，作为名为 `LUIS_APP_ID` 的环境变量。 设置公共 IoT 应用 **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** 的环境变量。 创建用于设置 `production` 发布槽的变量。

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. 使用密钥创建 msRest.ApiKeyCredentials 对象，并在终结点中使用该对象创建一个 [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 对象。

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>从运行时获取预测

添加以下方法以创建对预测运行时的请求。

用户言语是 [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) 对象的一部分。

**[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** 方法需要多个参数，如应用 ID、槽名称、用于满足请求的预测请求对象。 其他选项（如详细、显示所有意向和日志）都是可选的。

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>预测的主代码

使用以下主方法将变量和方法结合在一起，以获取预测。

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `node luis_prediction.js` 命令运行应用程序。

```console
node luis_prediction.js
```

预测结果返回一个 JSON 对象：

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>清理资源

完成预测后，请删除文件及其子目录，对此快速入门中的工作进行清理。
