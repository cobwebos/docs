---
title: 更改、训练应用，Node.js
titleSuffix: Azure Cognitive Services
description: 在本 Node.js 快速入门中，你将向家庭自动化应用中添加示例话语并训练该应用。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0f59a1b84b78bf5c35d11d79f56cb44d128f042e
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723053"
---
# <a name="quickstart-change-model-using-nodejs"></a>快速入门：使用 Node.js 更改模型

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* 包含 NPM 的最新 [**Node.js**](https://nodejs.org/en/download/)。
* 本文的 NPM 依赖项包括：[**request**](https://www.npmjs.com/package/request)、[**request-promise**](https://www.npmjs.com/package/request-promise)、[**fs-extra**](https://www.npmjs.com/package/fs-extra)。  
* [Visual Studio Code](https://code.visualstudio.com/)。

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>创建快速入门代码 

将 NPM 依赖项添加到名为 `add-utterances.js` 的文件。

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

将 LUIS 常量添加到文件。 复制以下代码，并将相应的占位符更改为自己的创作密钥、应用程序 ID 和版本 ID。

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

添加包含陈述的上传文件的名称和位置。 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

添加 `addUtterance` 函数的方法和对象。

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

添加 `train` 函数的方法和对象。

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

添加函数 `sendUtteranceToApi`，以发送和接收 HTTP 调用。 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

添加用于选择操作的 **main** 代码。

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>安装依赖项

创建具有以下文本的 `package.json` 文件：

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

在命令行上，从包含 package.json 的目录中使用 NPM 安装依赖项：`npm install`。

## <a name="run-code"></a>运行代码

使用 Node.js 在命令行中运行该应用程序。

调用 `npm start` 将添加话语、进行训练并获取训练状态。

```console
> npm start 
```

此命令行显示调用“添加陈述”API 的结果。 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，删除在本快速入门中创建的所有文件。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [以编程方式生成 LUIS 应用](luis-tutorial-node-import-utterances-csv.md)