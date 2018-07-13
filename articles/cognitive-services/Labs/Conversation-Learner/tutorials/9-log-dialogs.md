---
title: 如何在对话学习器应用程序中记录对话 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何在对话学习器应用程序中记录对话。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 477545c48aeca05d56fdae28ac65a8f381a482fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366305"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-application"></a>如何在对话学习器应用程序中记录对话

此教程介绍如何在对话学习器界面内执行最终用户测试；如何记录对话；如何更正已记录的对话以改善模型。

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息
可以使用记录的对话来评审并校正与最终用户执行的对话。  尤其是，可以修复实体标签和操作选择来改善定型模型和整体系统的性能。 

## <a name="steps"></a>步骤

### <a name="create-the-application"></a>创建应用程序

1. 在 Web UI 中，单击“新建应用”
2. 在“名称”中，输入“LogDialogs”。 然后单击“创建”。

### <a name="create-an-entity"></a>创建实体

1. 依次单击“实体”和“新建实体”。
2. 在“实体名称”中，输入“city”。
3. 单击“创建”。

### <a name="create-two-actions"></a>创建两个操作

1. 依次单击“操作”和“新建操作”
2. 在“响应”中，键入“Which city?”。
3. 在“取消实体资格”中，输入“$city”。
3. 单击创建

然后，创建第二个操作：

1. 依次单击“操作”和“新建操作”。
3. 在“响应”中，键入“The weather in $city is probably sunny”。
4. 在“必需实体”中，输入“$city”。
4. 单击“创建”。

现在有两个操作。

### <a name="train-the-bot"></a>定型机器人

1. 依次单击“定型”对话框和“新建定型”对话框。
2. 键入“what's the weather”。
3. 单击“评分操作”，然后选择“Which city?”
2. 输入“Seattle”。
3. 双击“Seattle”，并选择“city”。
    - 这会将其标记为“city”实体。
5. 单击“对操作评分”
6. 选择“The weather in $city is probably sunny”。
7. 单击“完成教学”。

添加另一个示例对话：

1. 依次单击“新建操作”和“新建定型对话”。
2. 键入“what's the weather in Seattle?”。 注意“Seattle”被标记为实体。
5. 单击“对操作评分” 
6. 选择“The weather in $city is probably sunny”。
7. 单击“完成教学”。

### <a name="try-the-bot-as-the-user"></a>以用户身份试用机器人
假设我们已对用户部署此机器人。

1. 单击“记录对话”。
2. 单击“新建聊天会话”。
    - 这将呈现机器人，其中用户将在 UI 左侧的 Web 聊天控件中与其进行交互。 可以忽略右侧的空白区域。
3. 键入“hello”。
4. 机器人响应：“which city?”
4. 键入“Boston”。
5. 机器人响应：“which city?”
    - 这似乎不正确。 那么我们保存此对话。
2. 单击“完成测试”。

启动新会话：

2. 单击“新建聊天会话”。
3. 键入“forecast for Boston”。
4. 机器人响应：“which city?”
2. 单击“完成教学”。

现在，我们来更正第二个对话：

1. 单击“记录对话”下的“forecast for Boston”。
    - 这将打开对话。
    - 如果单击用户方对话（这里是“forecast for Boston”），可以更改实体标签。
    - 如果单击系统方（这里是“which city”），可以更改所选的操作。
5. 单击“forecast for Boston”。 
    - 其根本原因是“Boston”未标记为实体。 需要做出更改。
    - 双击“Boston”，然后选择“city”。
    - 依次单击“提交更改”和“保存”。 将根据所做更改来创建定型对话，并转到做出更改时的定型对话。
6. 选择“The weather in $city is probably sunny”。
7. 单击“完成教学”。 如果现在转到“定型对话”，将发现添加了新操作。

![](../media/tutorial9_logdiag1.PNG)

现在，我们来更正其他对话：

1. 单击“记录对话”下的“hello”。
    - 这将打开对话。
3. 注意，对“hello”做出的响应是“which city”。 但是，我们想做出一些更改以使其更具有意义。 较好的答复是“hello, I'm the weather bot”等。 但没有操作来实现这一点，所以我们必须创建一个操作。
4. 单击“操作”。
    - 在“响应”中，键入“I'm the weather bot. I can help with forecasts.”
6. 取消选中“等待响应”复选框，使其成为非等待操作。
7. 单击“创建”。
8. 然后单击以选择此新操作。 然后单击“保存”。
    - 这将返回到定型会话中的相应时刻。
6. 单击以选择“which city?”
7. 键入“Boston”。 双击以将“Boston”标记为实体（如果尚未）。
8. 单击“对操作打分”。
9. 单击以选择“The weather in $city is probably sunny”。
10. 单击“完成教学”。

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实体检测回调](./10-entity-detection-callback.md)
