---
title: 如何通过对话学习器实体使用实体检测回调 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用实体检测回调。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 147887151abd5c1f7455b0efbf9aadbc2d884183
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796445"
---
# <a name="how-to-use-entity-detection-callback"></a>如何使用实体检测回调

本教程介绍实体检测回调，并说明了解析实体的常见模式。

## <a name="video"></a>视频

[![实体检测回调教程预览](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>要求
本教程要求运行 `tutorialEntityDetectionCallback` 机器人。

    npm run tutorial-entity-detection

## <a name="details"></a>详细信息
使用实体检测回调，可以通过代码修改实体识别行为以及对实体进行操作。 我们将通过完整介绍一个典型的实体检测回调设计模式来展示此功能。 例如，将“大苹果”解析为“纽约”。

## <a name="steps"></a>步骤

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”。
2. 在“名称”字段中键入“EntityDetectionCallback”，然后按 Enter。
3. 单击“创建”按钮。

此示例需要三个实体，因此，让我们来创建三个。

### <a name="create-the-custom-trained-entity"></a>创建自定义已训练实体

1. 在左侧面板中单击“实体”，然后单击“新建实体”按钮。
2. 选择“自定义训练”作为“实体类型”。
3. 键入“City”作为“实体名称”。
4. 单击“创建”按钮。

### <a name="create-the-first-programmatic-entity"></a>创建第一个编程实体

1. 单击“新建实体”按钮。
2. 选择“编程”作为“实体类型”。
3. 键入“CityUnknown”作为“实体名称”。
4. 单击“创建”按钮。

### <a name="create-the-first-programmatic-entity"></a>创建第一个编程实体

1. 单击“新建实体”按钮。
2. 选择“编程”作为“实体类型”。
3. 键入“CityResolved”作为“实体名称”。
4. 单击“创建”按钮。

现在，创建三个操作。

### <a name="action-creation"></a>创建函数

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“你需要哪个城市?”
3. 在“用户回应中的预期实体...”字段中，键入“City”。
4. 在“取消资格实体”字段中，键入“City”。
5. 单击“创建”按钮。
6. 单击“新建操作”按钮。
7. 在“机器人的响应...”字段中，键入“你需要哪个城市?”
8. 在“用户回应中的预期实体...”字段中，键入“我不知道这个城市”。
9. 单击“创建”按钮。
10. 单击“新建操作”按钮。
11. 在“机器人的响应...”字段中，键入“$CityResolved 非常好。”
12. 单击“创建”按钮。

下面是回调代码：

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中，在显示了“键入你的消息...”的位置键入“你好”
3. 单击“对操作打分”按钮。
4. 选择响应“你需要哪个城市?”
5. 在聊天面板中，在显示了“键入你的消息...”的位置键入“大苹果”
6. 单击“对操作打分”按钮。
    - 单击此按钮将触发实体检测回调
    - 回调代码将 CityResolved Entity 值正确设置为“纽约”
7. 选择响应“纽约非常好。”

此模式是许多机器人场景中的典型模式。 用户话语和提取的实体提供给你的业务逻辑，该逻辑将话语转变为规范形式，然后该规范形式保存到编程实体中供对话的后续轮次使用。

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [会话回调](./13-session-callbacks.md)
