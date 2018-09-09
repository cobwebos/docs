---
title: 使用 Javascript 在语言理解(LUIS) 中分析自然语言文本 - 认知服务 - Azure 认知服务 | Microsoft Docs
description: 本快速入门使用可用的公共 LUIS 应用从会话文本中确定用户的意向。 使用 Javascript 将用户的意向作为文本发送到公共应用的 HTTP 预测终结点。 在终结点处，LUIS 应用公共应用的模型来分析自然语言文本的含义，确定总体意向并提取与应用的主题域相关的数据。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 10210c3759611a77c4430a97896a19a6b97b9fa9
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "43768791"
---
# <a name="quickstart-analyze-text-using-javascript"></a>快速入门：使用 Javascript 分析文本

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>先决条件

* [Visual Studio Code](https://code.visualstudio.com/)
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>获取 LUIS 密钥

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>使用浏览器分析文本

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>使用 Javascript 分析文本 

可以使用 Javascript 来访问上一步骤中浏览器窗口显示的相同结果。 

1. 复制以下代码，并将其保存到某个 HTML 文件：

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. 在浏览器中打开该文件。 在窗体中，输入你的 LUIS 终结点密钥并选择“提交”。

    ![浏览器中显示的 Html 示例，其中显示了家庭自动化应用的 LUIS 结果](./media/luis-get-started-js-get-intent/html-results.png)

    结果显示在窗体下。 

## <a name="luis-keys"></a>LUIS 密钥

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清理资源

删除 Javascript 文件。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [添加话语](luis-get-started-javascript-add-utterance.md)
