---
title: 教程：了解如何使用 C# 调用语言理解 (LUIS) 应用 | Microsoft Docs
description: 本教程介绍如何使用 C# 调用 LUIS 应用。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 0416d19d27810a2ab8eeb20e16b2f921fc7826ee
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263483"
---
# <a name="tutorial-call-a-luis-endpoint-using-c"></a>教程：使用 C# 调用 LUIS 终结点

将陈述传递给 LUIS 终结点并取回意向和实体。

<!-- green checkmark -->
> [!div class="checklist"]
> * 创建 LUIS 订阅，并复制密钥值供以后使用
> * 在连接到公共示例 IoT 应用的浏览器中查看 LUIS 终结点结果
> * 创建 Visual Studio C# 控制台应用，以便对 LUIS 终结点发出 HTTPS 调用

<!-- link to free account -->本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="create-luis-subscription-key"></a>创建 LUIS 订阅密钥
1. 首先，需要在 Azure 门户中创建[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

2. 通过 https://portal.azure.com 登录到 Azure 门户。 

3. 遵循[使用 Azure 创建订阅密钥](./luis-how-to-azure-subscription.md)中的步骤获取密钥。

4. 返回 [LUIS](luis-reference-regions.md) 网站。 使用 Azure 帐户登录。 

    [![](media/luis-get-started-cs-get-intent/app-list.png "应用列表的屏幕截图")](media/luis-get-started-cs-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>了解 LUIS 返回的内容

若要了解 LUIS 应用返回的内容，可将示例 LUIS 应用的 URL 粘贴到浏览器窗口中。 示例应用是一个 IoT 应用，可检测用户是要打开还是关闭灯光。

1. 示例应用的终结点采用以下格式：`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light`请复制该 URL，并将 `subscription-key` 字段的值替换为自己的订阅密钥。
2. 将该 URL 粘贴到浏览器窗口中，然后按 Enter。 浏览器显示的 JSON 结果指示 LUIS 检测到 `HomeAutomation.TurnOn` 意向，以及值为 `bedroom` 的 `HomeAutomation.Room` 实体。

    ![JSON 结果指示检测到意向 TurnOn](./media/luis-get-started-cs-get-intent/turn-on-bedroom.png)
3. 将 URL 中的 `q=` 参数值更改为 `turn off the living room light`，然后按 Enter。 现在，结果指示 LUIS 检测到 `HomeAutomation.TurnOff` 意向，以及值为 `living room` 的 `HomeAutomation.Room` 实体。 

    ![JSON 结果指示检测到意向 TurnOff](./media/luis-get-started-cs-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-c"></a>在 C# 中通过终结点 API 使用 LUIS 结果 

可以使用 C# 来访问上一步骤中浏览器窗口显示的相同结果。 

1. 在 Visual Studio 中创建新的控制台应用程序。 复制以下代码，并将其保存到某个 *.cs 文件：
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/csharp/Program.cs)]
1. 请将 `subscriptionKey` 变量的值替换为自己的 LUIS 订阅密钥。

3. 在 Visual Studio 项目中，添加对 **System.Web** 的引用。

4. 运行控制台应用程序。 其中会显示前面在浏览器窗口中显示的相同 JSON。

![控制台窗口显示 LUIS 中的 JSON 结果](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="clean-up-resources"></a>清理资源
本教程中创建的两个资源是 LUIS 订阅密钥和 C# 项目。 请从 Azure 门户中删除 LUIS 订阅密钥。 关闭 Visual Studio 项目，并从文件系统中删除目录。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [添加表达](luis-get-started-cs-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website