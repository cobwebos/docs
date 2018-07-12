---
title: 如何通过对话学习器应用程序使用等待和非等待操作 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器应用程序使用等待和非等待操作。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb2cbd55b6c8be51213095926bb592169613d1fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366249"
---
# <a name="wait-and-non-wait-actions"></a>等待和非等待操作

本教程介绍对话学习器中等待和非等待操作间的区别。

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息

- 等待操作：执行“等待”操作后，系统将停止执行操作并等待用户输入。
- 非等待操作：执行“非等待”操作后，系统将立即选择另一操作（无需等待用户先行输入）。

## <a name="steps"></a>步骤

### <a name="create-a-new-app"></a>创建新应用

1. 在 Web UI 中，单击“新建应用”
2. 在“名称”中，输入 WaitNonWait。 然后单击“创建”。

### <a name="create-the-first-wait-action"></a>创建首个等待操作

1. 依次单击“操作”和“新建操作”。
2. 在“响应”中，输入“你想要哪种动物？”。
    - 这是一个等待操作，因此请选中“等待响应”复选框。
3. 单击“完成”。

### <a name="create-a-non-wait-action"></a>创建非等待操作

1. 单击“新建操作”
2. 在“响应”中，键入“牛哞哞叫”。
3. 取消选中“等待响应”复选框。
4. 单击创建

### <a name="create-a-second-non-wait-action"></a>创建第二个“非等待”操作

1. 单击“新建操作”
2. 在“响应”中，键入“鸭子嘎嘎叫”。
3. 取消选中“等待响应”复选框。
4. 单击创建

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>训练机器人

1. 依次单击“训练对话”和“新建训练对话”。
2. 键入“你好”
3. 单击“对操作打分”，然后选择“你想要哪种动物?”。
4. 输入“牛”
5. 单击“对操作打分”，然后选择“牛哞哞叫”。
    - 请注意，机器人不会等待输入，而是会执行下一操作。
2. 选择“你想要哪种动物?”。
3. 输入“鸭子”
5. 单击“对操作打分”，然后选择“鸭子嘎嘎叫”。

![](../media/tutorial2_dialogs.PNG)

请注意机器人就等待和非等待操作的响应顺序。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实体简介](./3-introduction-to-entities.md)
