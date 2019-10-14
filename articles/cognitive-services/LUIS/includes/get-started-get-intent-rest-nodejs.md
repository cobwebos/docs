---
title: 使用 Node.js 通过 REST 调用获取意向
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: a6dfe21cd92c5bf5580d7b121f33f68fb4e135fa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838543"
---
## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org/) 编程语言 
* [Visual Studio Code](https://code.visualstudio.com/)
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> [**Azure-Samples** GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node)中提供了完整的 Node.js 解决方案。

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

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

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，关闭 Visual Studio 项目并从文件系统中删除项目目录。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Node.js 添加话语并进行训练](../luis-get-started-node-add-utterance.md)