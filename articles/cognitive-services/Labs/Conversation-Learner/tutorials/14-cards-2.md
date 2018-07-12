---
title: 如何通过对话学习器应用程序使用卡，第 2 部分 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器应用程序使用卡。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 254f0953fd3e281a35857e69d9795e3decebf45d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366255"
---
# <a name="how-to-use-cards-part-1-of-2"></a>如何使用卡（第 1 部分，共 2 部分）
本教程介绍如何向机器人添加可填写的卡窗体。 该窗体将显示表单域如何转为实体。

对话学习器要求将卡片定义文件置于名为“cards”的目录中，该目录位于机器人启动的目录。

## <a name="requirements"></a>要求
此教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息

卡是 UI 元素，可让用户选择对话中的选项。 

### <a name="open-the-demo"></a>打开演示

在 Web UI 的“应用”列表中，单击 Tutorial-14-Cards-2。 

### <a name="the-card"></a>卡

卡定义位于以下位置：C:\<installedpath\>\src\cards\shippingAddress.json。

此卡可收集送货地址的三个字段：城市、街道和省/自治区/直辖市。

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>操作

我们创建了三个操作。 如下所示，第一个操作是卡。

![](../media/tutorial14_actions.PNG)

我们来看看如何创建卡操作类型：

- 注意“地址-街道”，其类型是 Input.text 及其 ID。
- 同样，还有“地址-城市”和包含“地址-省/自治区/直辖市”ID 的下拉列表。

ID 非常重要，因为填充和提交字段时，这些是实体名称，会在机器人中接收这些值。

## <a name="entities"></a>实体
我们已定义了三个与卡匹配的实体，如上所述。

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>操作

我们定义了两个操作。

![](../media/tutorial14_actions.PNG)

- 第一个是送货地址卡，其中“操作类型”为卡，且从下拉列表中选择了模板作为 shippingAddress。
- 第二个是一个简单操作，用以读回送货地址。

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>训练对话

我们来看一个教学对话。

1. 依次单击“训练对话”和“新建训练对话”。
1. 输入“您好”。
2. 单击“对操作打分”。
3. 单击以选择“送货地址”。
4. 填写卡并提交。
    - 请注意，这些值现已被移动到实体内存。 表单已拆分了输入，因此无需进行解析。
5. 单击“对操作打分”。
3. 单击以选择“送至 $Address…”。
4. 单击“完成测试”。

![](../media/tutorial14_train_dialog.PNG)

你现在已经了解了如何从含有可填充字段和下拉列表的卡中获取值，并在机器人实体中捕获和收集这些值。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [分支和撤消](./15-branching-and-undo.md)
