---
title: Conversation Learner 模型中的实体解析程序 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何在 Conversation Learner 中使用实体解析程序。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e6a671470a87f4509e466bcdfe7845b7bf7ec8dc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209139"
---
# <a name="entity-resolvers"></a>实体解析程序

本教程介绍如何在 Conversation Learner 中使用实体解析程序

## <a name="video"></a>视频

[![实体解析程序教程预览](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息

- “解析程序类型”是自定义实体的可选属性。
- 实体解析程序利用 LUIS 中预先训练的实体识别器的功能，为自定义实体提供更多详细信息，使用户对其更加了解。

## <a name="steps"></a>Steps

### <a name="create-a-new-model"></a>创建新模型

1. 在 Web UI 中，单击“新建模型”按钮。
2. 在“名称”字段中键入“实体解析程序”，然后按 Enter 或单击“创建”按钮。

### <a name="create-a-pair-of-entities"></a>创建一对实体

1. 在左侧面板中单击“实体”，然后单击“新建实体”按钮。
2. 在“实体名称”字段中键入“离开”。
3. 在“解析程序类型”下拉列表中，选择“datetimeV2”。
4. 单击“创建”按钮。
5. 在阅读弹出的信息后，单击“确定”按钮。
6. 执行相同的步骤，创建另一个名为“返回”的实体，该实体的解析程序类型也为“datetimeV2”。

### <a name="create-a-pair-of-actions"></a>创建一对操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应”字段中，键入“你在 $departure 离开，在 $return 返回”。
    - 重要说明 - 键入“$[entityName]”时需按 Enter 或单击下拉列表中的实体，否则 Conversation Learner 会将其视为文本而非实体。
    - 请注意，“所需实体”字段也会获得这些实体，这些实体不能删除。 这样会导致此操作不可用，直至两个所需的实体都存在。
3. 单击“创建”按钮。
4. 再次单击“新建操作”按钮，创建第二个操作。
5. 在“机器人的响应”字段中，键入“你打算何时旅行？”。
6. 在“取消实体资格”字段中键入“离开”，同时键入“返回”。
    - 这是告知机器人，如果这两个实体中的任一个包含值，则不执行此操作。
7. 单击“创建”按钮。


### <a name="training"></a>培训

1. 观察页面左上部分的“训练: [状态]”，等待它变为“已完成”。
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
    - 将鼠标悬停在每个实体上面，观察这些实体作为日期对象的情况。这些对象会明确地捕获实际的日历日期，而不是“星期天”或“明天”。
10. 选择“你的离开日期为...”机器人响应。
11. 单击“保存”按钮。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [备用输入](./10-alternative-inputs.md)
