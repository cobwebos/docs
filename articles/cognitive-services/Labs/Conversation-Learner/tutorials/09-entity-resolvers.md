---
title: 对话学习器模型-Microsoft 认知服务中的实体冲突解决程序 |Microsoft Docs
titleSuffix: Azure
description: 了解如何在 Conversation Learner 中使用实体解析程序。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 7b5f5645a8b5d1d993f87d18503fe270f42c5de5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390025"
---
# <a name="entity-resolvers"></a>实体解析程序

本教程介绍如何在 Conversation Learner 中使用实体解析程序

## <a name="video"></a>视频

[![实体解析程序教程预览](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>要求
本教程要求常规教程机器人处于运行状态

    npm run tutorial-general

## <a name="details"></a>详细信息

- “解析程序类型”是自定义实体的可选属性。
- 实体解析程序利用 LUIS 中预先训练的实体识别器的功能，为自定义实体提供更多详细信息，使用户对其更加了解。

## <a name="steps"></a>Steps

从 Web UI 的主页开始。

### <a name="create-the-model"></a>创建模型

1. 选择**新的模型**。
2. 输入**实体解决**有关**名称**。
3. 选择“创建”  。

### <a name="create-a-pair-of-entities"></a>创建一对实体

1. 选择**实体**在左侧的窗格中，然后**新实体**。
2. 输入**出发**有关**实体名称**。
3. 选择**datetimeV2**有关**冲突解决程序类型**。
4. 选择“创建”  。 通过选择关闭信息性弹出项**确定**。
5. 重复步骤 1-4，创建名为的第二个实体**返回**与**datetimeV2**冲突解决程序类型。

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>创建一对操作

1. 选择**操作**在左侧的窗格中，然后**新操作**。
2. 输入**都将保留上 $departure 和返回上 $return**为**智能机器人应用程序的响应...** .
    - 重要-键入 [entityName] 需要按输入或单击下拉列表否则为对话学习器中的实体将认为这是文本而不是实体时。
    - 请注意，**所需实体**字段还将获得这些实体并且不能删除它们。 这样会导致此操作不可用，直至两个所需的实体都存在。
3. 选择“创建”  。
4. 选择**新的操作**若要创建第二个操作。
5. 输入**当你是否打算旅行？** 为**智能机器人应用程序的响应...** .
6. 输入**出发**并**返回**有关**取消实体**。 这是告知机器人，如果这两个实体中的任一个包含值，则不执行此操作。
7. 选择“创建”  。

![](../media/T09_actions.png)

### <a name="training"></a>培训

1. 观看**定型: [状态]** 中的左上角**已完成**。
    - 如果等待时间过长，可以单击“刷新”链接。
    - 训练状态必须是“已完成”，否则在训练模型时实体解析程序将无法运行。

2. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
3. 键入第一个用户话语“为我预订航班”。 
4. 单击“对操作打分”按钮。
5. 选择响应“你打算何时旅行？”。
6. 以用户身份使用“明天离开，下周日返回”进行响应。
    - 注意 Conversation Learner 如何检测到该用户轮次的两个“预先训练的日期”实体。
7. 在“实体检测”面板中，选择文本“明天”，将其标记为“离开”
8. 另请将文本“下周日”标记为“返回”
9. 单击“对操作打分”按钮。
    - 注意“备忘”窗格包含离开和返回日期的情况。
    - 悬停在每个并观察如何将实体是清楚地捕获实际的日历日期而不是"Sunday"或"tomorrow"的日期对象。
10. 选择“你的离开日期为...”机器人响应。
11. 单击“保存”按钮。

![](../media/T09_training.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [备用输入](./10-alternative-inputs.md)
