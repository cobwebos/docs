---
title: 获取意向，Go
titleSuffix: Language Understanding - Azure Cognitive Services
description: 此 Go 快速入门使用可用的公共 LUIS 应用从会话文本中确定用户的意向。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 6c74e16c55a1b4050ee02c96a1728a07261ff6c3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276126"
---
# <a name="quickstart-get-intent-using-go"></a>快速入门：使用 Go 获取意向

在本快速入门中，你将向 LUIS 终结点传递话语并返回意向和实体。

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>先决条件

* [Go](https://golang.org/) 编程语言  
* [Visual Studio Code](https://code.visualstudio.com/)
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>使用浏览器获取意向

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>以编程方式获取意向

可以使用 GO 来访问上一步骤中浏览器窗口显示的相同结果。 

1. 创建名为 `endpoint.go` 的新文件。 添加以下代码：
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. 在你创建该文件的同一目录中，在命令提示符下输入 `go build endpoint.go` 来编译 Go 文件。 对于成功的生成，命令提示符不会返回任何信息。

3. 通过在命令提示符下输入以下文本从命令行运行 Go 应用程序： 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    将 `<add-your-key>` 替换为你的密钥的值。  
    
    命令提示符响应为： 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```
    
## <a name="luis-keys"></a>LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清理资源
关闭 Go 文件并将其从文件系统中删除。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [添加表达](luis-get-started-go-add-utterance.md)
