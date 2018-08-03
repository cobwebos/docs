---
title: 如何通过对话学习器模型使用多值实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用多值实体。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6193a515f0d8136e0d420b7554cf26fee8f50953
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173095"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>如何通过对话学习器模型使用多值实体
本教程介绍实体的“多值”属性。

## <a name="video"></a>视频

[![教程 6 预览](http://aka.ms/cl-tutorial-06-preview)](http://aka.ms/blis-tutorial-06)

##<a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息
“多值”实体将值累积到列表中，而不是存储单个值。  这样用户可以在实体中指定多个值，如披萨上的浇汁。

具体而言，如果实体标记为“多值”，则该实体的每个识别实例将追加到机器人内存的列表中（而不是覆盖单个实体值）。

## <a name="steps"></a>Steps

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”
2. 在“名称”中，输入 MultiValueEntities。 然后单击“创建”。

### <a name="create-an-entity"></a>创建实体

1. 依次单击“实体”和“新建实体”。
2. 在“实体名称”中，输入“浇汁”。
3. 选中“多值”。
    - 多值实体会将一个或多个值累积到实体中。
2. 选中“可否定”。  
    - 这将允许用户从其累积的披萨浇汁列表中删除浇汁。
3. 单击“创建”。

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>创建两个操作

1. 依次单击“操作”和“新建操作”
2. 在响应中，键入“你需要哪些浇汁?”。
3. 在“取消实体资格”中，输入“浇汁”。
3. 单击创建

然后，创建第二个操作。

1. 依次单击“操作”和“新建操作”以创建第二个操作。
3. 在响应中，键入“下面是你的浇汁: $Toppings”。
4. 单击创建

现在已有两个操作。

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>定型机器人

1. 依次单击“定型”对话框和“新建定型”对话框。
2. 键入“hello”。
3. 单击“对操作打分”，然后选择“你需要哪些浇汁?”
2. 输入“蘑菇和奶酪”。 
    - 可以标记零个、一个或多个实体。
3. 单击“蘑菇”，然后选择“浇汁”。
4. 单击“奶酪”，然后选择“浇汁”。
5. 单击“对操作评分”
    - Toppings 实体中现存在两个值。 
6. 选择“下面是你的浇汁: $Toppings”。

我们可以向其中添加更多内容：

7. 输入“添加辣椒”。
    - 单击实体检测下的“辣椒”，然后选择“浇汁”。
3. 单击“对操作打分”。
    - “pepper”现在显示为配料中的其他值。
6. 选择“下面是你的浇汁: $Toppings”。

让我们删除一个浇汁并添加另一个浇汁：

2. 键入“删除辣椒并添加香肠”。
1. 单击“辣椒”并单击红色 x 以将其删除。
2. 单击“辣椒”，然后选择“-浇汁”。
3. 单击“对操作打分”。
    - 已删除“pepper”并添加了“sausage”。
6. 选择“下面是你的浇汁: $Toppings”。

现在让我们尝试删除所有内容：

6. 输入“删除蘑菇、删除奶酪并删除香肠”。
7. 单击这三个浇汁的每一个，然后选择“-浇汁”。
7. 单击“对操作打分”。
    - 清除所有配料。
2. 选择“你需要哪些浇汁?”
3. 单击“完成教学”

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [内置实体](./7-built-in-entities.md)
