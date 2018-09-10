---
title: 使用 Ruby 在语言理解(LUIS) 中分析自然语言文本 - 认知服务 - Azure 认知服务 | Microsoft Docs
description: 本快速入门使用可用的公共 LUIS 应用从会话文本中确定用户的意向。 使用 Ruby 将用户的意向作为文本发送到公共应用的 HTTP 预测终结点。 在终结点处，LUIS 应用公共应用的模型来分析自然语言文本的含义，确定总体意向并提取与应用的主题域相关的数据。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 0909c1dd056570a275b3042674d251c637413cae
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157695"
---
# <a name="quickstart-analyze-text-using-ruby"></a>快速入门：使用 Ruby 分析文本

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>先决条件

* [Ruby](https://www.ruby-lang.org/) 编程语言
* [Visual Studio Code](https://code.visualstudio.com/)
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

<a name="create-luis-subscription-key"></a>

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>使用浏览器分析文本

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-ruby"></a>使用 Ruby 分析文本 

可以使用 Ruby 来访问上一步骤中浏览器窗口显示的相同结果。 

1. 复制以下代码，并将其保存到名为 `endpoint-call.rb` 的文件中：

   [!code-ruby[Ruby code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/ruby/endpoint-call.rb)]

2. 将 `"YOUR-KEY"` 替换为终结点密钥。

3. 使用 `ruby endpoint-call.rb` 在命令行中运行 Ruby 应用程序。 其中会显示前面在浏览器窗口中显示的相同 JSON。

    ```
    LUIS query: turn on the left light
    
    Request URI: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn+on+the+left+light&timezoneOffset=0&verbose=false&spellCheck=false&staging=false
    
    JSON Response:
    
    {
      "query": "turn on the left light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.933549
      },
      "entities": [
        {
          "entity": "left",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 15,
          "score": 0.540835142
        }
      ]
    }
```

## <a name="clean-up-resources"></a>清理资源

删除 Ruby 文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加表达](luis-get-started-ruby-add-utterance.md)