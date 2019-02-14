---
title: 获取意向，Python
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在本快速入门中，你将向 LUIS 终结点传递话语并返回意向和实体。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 043b31aaaad30e9f2663b0a53273874e8c84ff6d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884012"
---
# <a name="quickstart-get-intent-using-python"></a>快速入门：使用 Python 获取意向
在本快速入门中，你将向 LUIS 终结点传递话语并返回意向和实体。

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>先决条件

* [Python 3.6](https://www.python.org/downloads/) 或更高版本。
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>使用浏览器获取意向

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>以编程方式获取意向

可以使用 Python 来访问上一步骤中浏览器窗口显示的相同结果。

1. 将以下代码片段之一复制到名为 `quickstart-call-endpoint.py` 的文件中：

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. 将 `Ocp-Apim-Subscription-Key` 字段的值替换为你的 LUIS 终结点密钥。

3. 使用 `pip install requests` 安装依赖项。

4. 使用 `python ./quickstart-call-endpoint.py` 运行该脚本。 其中会显示前面在浏览器窗口中显示的相同 JSON。

## <a name="luis-keys"></a>LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清理资源
删除 python 文件。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加表达](luis-get-started-python-add-utterance.md)
