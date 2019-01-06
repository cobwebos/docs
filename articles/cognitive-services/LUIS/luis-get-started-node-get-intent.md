---
title: 获取意向，Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: 本快速入门使用可用的公共 LUIS 应用从会话文本中确定用户的意向。 使用 Node.js 将用户的意向作为文本发送到公共应用的 HTTP 预测终结点。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 3a16c87e96a94fcf95694d3cbfd6f8f07adedb74
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717065"
---
# <a name="quickstart-get-intent-using-nodejs"></a>快速入门：使用 Node.js 获取意向

在本快速入门中，你将向 LUIS 终结点传递话语并返回意向和实体。

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org/) 编程语言 
* [Visual Studio Code](https://code.visualstudio.com/)
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> [**Azure-Samples** GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node)中提供了完整的 Node.js 解决方案。

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>使用浏览器获取意向

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>以编程方式获取意向

可以使用 Node.js 来访问上一步骤中浏览器窗口显示的相同结果。

1. 复制以下代码片段：

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. 创建包含以下文本的 `.env` 文件，或者在系统环境中设置以下变量：

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. 将 `LUIS_ENDPOINT_KEY` 环境变量设置为你的密钥。

4. 通过在命令行上运行以下命令来安装依赖项：`npm install`。

5. 通过 `npm start` 运行代码。 其中会显示前面在浏览器窗口中显示的相同值。

## <a name="luis-keys"></a>LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清理资源

删除 Node.js 文件。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [添加表达](luis-get-started-node-add-utterance.md)
