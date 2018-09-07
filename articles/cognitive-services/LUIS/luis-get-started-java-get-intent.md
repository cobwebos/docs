---
title: 使用 Java 在语言理解(LUIS) 中分析自然语言文本 - 认知服务 - Azure 认知服务 | Microsoft Docs
description: 本快速入门使用可用的公共 LUIS 应用从会话文本中确定用户的意向。 使用 Java 将用户的意向作为文本发送到公共应用的 HTTP 预测终结点。 在终结点处，LUIS 应用公共应用的模型来分析自然语言文本的含义，确定总体意向并提取与应用的主题域相关的数据。
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: 559c0e5832249b095b923fe88467f8f4c5ffa5e1
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "43768799"
---
# <a name="quickstart-analyze-text-using-java"></a>快速入门：使用 Java 分析文本

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>先决条件

* [JDK SE](http://www.oracle.com/technetwork/java/javase/downloads/index.html)（Java 开发工具包，标准版）
* [Visual Studio Code](https://code.visualstudio.com/)
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>使用浏览器分析文本

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-java"></a>使用 Java 分析文本 

可以使用 Java 来访问上一步骤中浏览器窗口显示的相同结果。 

1. 复制以下代码以在名为 `LuisGetRequest.java` 的文件中创建一个类：

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. 将 `YOUR-KEY` 变量的值替换为自己的 LUIS 密钥。

3. 使用 `javac -cp ":lib/*" LuisGetRequest.java` 编译 java 程序。 

4. 使用 `java -cp ":lib/*" LuisGetRequest.java`运行应用程序。 其中会显示前面在浏览器窗口中显示的相同 JSON。

    ![控制台窗口显示 LUIS 中的 JSON 结果](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>LUIS 密钥

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清理资源

删除 Java 文件。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [添加表达](luis-get-started-java-add-utterance.md)