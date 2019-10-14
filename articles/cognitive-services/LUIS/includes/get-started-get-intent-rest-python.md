---
title: 使用 Python 通过 REST 调用获取意向
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e86d1e16e7c61f851a75ad97d2744b0daa009617
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838521"
---
## <a name="prerequisites"></a>先决条件

* [Python 3.6](https://www.python.org/downloads/) 或更高版本。
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent--programmatically"></a>以编程方式获取意向

可以使用 Python 来访问上一步骤中浏览器窗口显示的相同结果。

1. 将以下代码片段之一复制到名为 `quickstart-call-endpoint.py` 的文件中：

    #### <a name="python-27tabp2"></a>[Python 2.7](#tab/P2)

    [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]    

    #### <a name="python-36tabp3"></a>[Python 3.6](#tab/P3)

    [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

    * * *

1. 将 `Ocp-Apim-Subscription-Key` 字段的值替换为你的 LUIS 终结点密钥。

1. 使用 `pip install requests` 安装依赖项。

1. 使用 `python ./quickstart-call-endpoint.py` 运行该脚本。 其中会显示前面在浏览器窗口中显示的相同 JSON。

## <a name="luis-keys"></a>LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，关闭 Visual Studio 项目并从文件系统中删除项目目录。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Python 添加话语并进行训练](../luis-get-started-python-add-utterance.md)