---
title: 在 10 分钟内创建第一个语言理解 (LUIS) 应用 - 认知服务 LUIS | Microsoft Docs
description: 在本快速入门中，请使用预生成的域 `HomeAutomation` 来创建 LUIS 应用，以便打开和关闭灯和设备。 此预生成的域为你提供意向、实体和示例话语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: diberry
ms.openlocfilehash: 457f23936dec0cf85e9aebbf3e54bba37c2f3ca3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2018
ms.locfileid: "43768781"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>快速入门：使用预生成的家庭自动化应用

在本快速入门中，请使用预生成的域 `HomeAutomation` 来创建 LUIS 应用，以便打开和关闭灯和设备。 此预生成的域为你提供意向、实体和示例话语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点。

## <a name="prerequisites"></a>先决条件

本文需要一个在 [http://www.luis.ai](http://www.luis.ai) 的 LUIS 门户中创建的免费 LUIS 帐户。 

## <a name="create-a-new-app"></a>创建新应用
可在“我的应用”中创建和管理应用程序。 

1. 登录到 LUIS 门户。

2. 选择“创建新应用”。

    [![](media/luis-quickstart-new-app/app-list.png "应用列表的屏幕截图")](media/luis-quickstart-new-app/app-list.png)

3. 在对话框中，将应用程序命名为“家庭自动化”。

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "“创建新应用”弹出对话框的屏幕截图")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. 选择应用程序区域性。 对于此家庭自动化应用，请选择“中文”。 然后选择“完成”。 LUIS 会创建家庭自动化应用。 

    >[!NOTE]
    >创建应用程序后将无法更改区域性。 

## <a name="add-prebuilt-domain"></a>添加预生成域

在左侧导航窗格中选择“预生成的域”。 然后搜索“家庭”。 选择“添加域”。

[![](media/luis-quickstart-new-app/home-automation.png "在预生成域菜单中调用的“家庭自动化”域的屏幕截图")](media/luis-quickstart-new-app/home-automation.png)

成功添加域以后，预生成域框会显示“删除域”按钮。

[![](media/luis-quickstart-new-app/remove-domain.png "包含删除按钮的“家庭自动化”域的屏幕截图")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>意向和实体

在左侧导航窗格中选择“意向”，以便查看 HomeAutomation 域意向。 

[![](media/luis-quickstart-new-app/home-automation-intents.png "“意向”列表的屏幕截图，突出显示了表中的意向名称")](media/luis-quickstart-new-app/home-automation-intents.png)

每个意向都有示例话语。

> [!NOTE]
> “无”是由所有 LUIS 应用提供的意向。 可以使用它来处理与应用提供的功能无法对应的话语。 

选择 **HomeAutomation.TurnOff** 意向。 可以看到，此意向包含一系列使用实体标记的话语。

[![](media/luis-quickstart-new-app/home-automation-turnon.png "HomeAutomation.TurnOff 意向的屏幕截图")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-your-app"></a>训练用户

选择顶部导航栏中的“训练”。

[![](media/luis-quickstart-new-app/trained.png "HomeAutomation.TurnOff 意向的屏幕截图，其中包含绿色的成功通知")](media/luis-quickstart-new-app/trained.png)

## <a name="test-your-app"></a>测试应用
训练完应用以后，即可测试它。 选择顶部导航栏中的“测试”。 在“交互式测试”窗格中键入“关灯”之类的测试话语，然后按 Enter。 

```
Turn off the lights
```

检查评分最高的意向是否对应于每个测试话语的预期意向。

在以下示例中，“关灯”被正确标识为“HomeAutomation.TurnOff”的评分最高的意向。

[![](media/luis-quickstart-new-app/test.png "“测试”面板的屏幕截图，其中突出显示了话语")](media/luis-quickstart-new-app/test.png)


再次选择“测试”，折叠测试窗格。 

## <a name="publish-your-app"></a>发布应用
在顶部导航栏中，选择“发布”。 

[![](media/luis-quickstart-new-app/publish.png "应用的屏幕截图，其中突出显示了发布按钮")](media/luis-quickstart-new-app/publish.png)

选择“生产”槽和“发布”按钮。

顶部的绿色通知栏指示应用已成功发布。

[![](media/luis-quickstart-new-app/published.png "已成功发布的应用的屏幕截图")](media/luis-quickstart-new-app/published.png)

成功发布以后，即可使用显示在“发布应用”页中的终结点 URL。

[![](media/luis-quickstart-new-app/endpoint.png "发布页的屏幕截图，其中已突出显示终结点 URL")](media/luis-quickstart-new-app/endpoint.png)

## <a name="use-your-app"></a>使用应用
可以使用生成的 URL 在浏览器中测试已发布的终结点。 在浏览器中打开此 URL，将 URL 参数“&q”设置为测试查询。 例如，将 `turn off the living room light` 添加到 URL 末尾，然后按 Enter。 浏览器会显示 HTTP 终结点的 JSON 响应。


[![](media/luis-quickstart-new-app/turn-off-living-room.png "浏览器的屏幕截图，其中的 JSON 结果检测到意向 TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，请在应用列表中选择应用名称右侧的省略号 (...) 按钮，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

可以从代码调用终结点：

> [!div class="nextstepaction"]
> [使用代码调用 LUIS 终结点](luis-get-started-cs-get-intent.md)
