---
title: Conversation Learner 模型中的实体冲突解决-Azure 认知服务 |Microsoft Docs
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
ROBOTS: NOINDEX
ms.openlocfilehash: 60c4abf1590cb91fd460cc6a2a5ba75a225ebd80
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704044"
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

## <a name="steps"></a>步骤

从 Web UI 的主页开始。

### <a name="create-the-model"></a>创建模型

1. 选择 "**新建模型**"。
2. 输入**名称**的**实体解析**程序。
3. 选择“创建”。

### <a name="create-a-pair-of-entities"></a>创建一对实体

1. 在左侧面板中选择 "**实体**", 然后选择 "**新建实体**"。
2. 输入**实体名称**的**出发**。
3. 为**解析程序类型**选择**datetimeV2** 。
4. 选择“创建”。 通过选择 **"确定"** 关闭信息性弹出窗口。
5. 重复步骤 1-4, 以使用**datetimeV2**解析程序类型创建名为**return**的第二个实体。

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>创建一对操作

1. 在左侧面板中选择 "**操作**", 然后选择 "**新建操作**"。
2. 输入 **$departure 并返回**到**机器人响应**的 $return 。
    - 重要提示-在 $ [entityName] 中键入内容时, 需要按 enter 或单击下拉图中的实体, 否则 Conversation Learner 会将此项视为文本而不是实体。
    - 请注意, "**必需的实体**" 字段还将获取这些实体, 并且不能删除它们。 这样会导致此操作不可用，直至两个所需的实体都存在。
3. 选择“创建”。
4. 选择 "**新建操作**" 以创建第二个操作。
5. 输入**你计划旅行的时间？** **机器人的响应 ...**
6. 进入**出发**并为**Disqualifying 实体** **返回**。 这是告知机器人，如果这两个实体中的任一个包含值，则不执行此操作。
7. 选择“创建”。

![](../media/T09_actions.png)

### <a name="training"></a>培训

1. 观看左上角的**培训: [Status]** **完成**。
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
    - 将鼠标悬停在每个对象上, 并观察实体是 date 对象的方式, 这些对象清楚地捕获实际日历日期, 而不是 "星期日" 或 "明天"。
10. 选择“你的离开日期为...”机器人响应。
11. 单击“保存”按钮。

![](../media/T09_training.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [枚举实体](./tutorial-enum-set-entity.md)
