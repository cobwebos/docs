---
title: 使用 C# 在语言理解 (LUIS) 中分析自然语言文本 - Azure 认知服务 | Microsoft Docs
description: 本快速入门使用可用的公共 LUIS 应用从会话文本中确定用户的意向。 使用 C# 将用户的意向作为文本发送到公共应用的 HTTP 预测终结点。 在终结点处，LUIS 应用公共应用的模型来分析自然语言文本的含义，确定总体意向并提取与应用的主题域相关的数据。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: b6ddd48fc6bfa5c099e42f3717a2113f871b4f9a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163254"
---
# <a name="quickstart-analyze-text-using-c"></a>快速入门：使用 C# 分析文本

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>先决条件

* [Visual Studio Community 2017 edition](https://visualstudio.microsoft.com/vs/community/)
* C# 编程语言（包括在 VS Community 2017 中）
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>使用浏览器分析文本

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-c"></a>使用 C# 分析文本 

使用 C# 来查询预测终结点 GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) 以获取与在前面部分中在浏览器窗口看到的结果相同的结果。 

1. 在 Visual Studio 中创建新的控制台应用程序。 

    ![LUIS 用户设置菜单访问](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. 在 Visual Studio 项目中，在解决方案资源管理器中，选择“添加引用”，然后从“程序集”选项卡中选择“System.Web”。

    ![LUIS 用户设置菜单访问](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. 将 Program.cs 改写为以下代码：
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. 将 `YOUR_KEY` 的值替换为你自己的 LUIS 密钥。

5. 生成并运行控制台应用程序。 其中会显示前面在浏览器窗口中显示的相同 JSON。

    ![控制台窗口显示 LUIS 中的 JSON 结果](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，关闭 Visual Studio 项目并从文件系统中删除项目目录。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 C# 添加话语并进行训练](luis-get-started-cs-add-utterance.md)