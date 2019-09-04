---
title: 何时使用枚举实体并使用 Conversation Learner 模型设置实体操作-Azure 认知服务 |Microsoft Docs
titleSuffix: Azure
description: 了解何时适合使用枚举实体并使用 Conversation Learner 模型设置实体操作。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 429570d81f7e15758d8ea60951bb4d01b96f8f2c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256483"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>何时使用枚举实体并设置实体操作

本教程将说明何时应使用 ENUM （枚举）实体和 SET_ENTITY 操作。

## <a name="video"></a>视频

[![设置实体教程预览](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>涵盖的内容

本教程将介绍两个新功能。 新类型的实体（称为枚举）和称为 SET_ENTITY 的一种新的操作类型，它可以引用这些枚举值之一，如名称所示，将实体设置为此值。 如下所示，这些新功能一起使用，我们将说明它们的定义以及如何使用它们。 在获取详细信息之前，请务必了解这些功能可帮助解决哪些问题。

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>问题

在会话中，词的含义取决于上下文。  通常，使用语言理解服务学习和提取标记关键字，但在这些情况下，这些系统可能无法使用标记的示例进行学习。

假设你在附近的人员之间 overhear 了对话的一部分，并且你只听说了 "是" 一词。 您不知道 "是" 同意或确认的原因，因为您没有听到提出的问题。 询问的问题是上下文，这为答案提供了意义。 同样，由于 "是" 是对许多不同问题的常见响应，因此，提供的示例与[自定义](04-introduction-to-entities.md)的已训练实体一样，就不能了解它，因为这样可以将每个 "是" 作为该实体进行标记。

### <a name="example"></a>示例

让我们进一步阐明下面的示例：

机器人你喜欢 Azure 认知服务吗？
用户：是机器人：是否喜欢冰激凌？
用户：是

在前面的教程中，我们查看了[自定义](04-introduction-to-entities.md)的已训练实体，你的初始想法可能是创建一个名为 "likesCogServices" 的实体，并将第一个 "是" 标记为此实体。  但是，系统还会将第二个 "是" 标记为。 当我们尝试将第二个 "是" 的标签更正为 "likesIceCream" 时，我们将创建两个相同输入 "是" 的冲突，这表示不同的内容，会停滞。

在这些情况下，需要使用枚举实体和 SET_ENTITY 操作。

## <a name="when-to-use-enums-or-set_entity-actions"></a>何时使用枚举或 SET_ENTITY 操作

使用以下规则来了解何时使用枚举实体和 SET_ENTITY 操作：

- 实体的检测或设置与上下文相关
- 可能值的数目是固定的（是，不会有两个值）

换句话说，将其用于任何关闭的提示，例如始终导致 "是" 或 "否" 的确认问题。

> [!NOTE]
> 目前，每个枚举实体最多可以有5个值的限制。 每个值使用当前64限制中的一个槽。 请参阅[cl-值和边界](../cl-values-and-boundaries.md)

例如：机器人订单是否正确？
用户：是

如果实体的可能值为 "已结束" 且未固定，则需要使用替代功能，如 "[预期实体](05-expected-entity.md)"。

例如：机器人你叫什么名字？
用户：Matt 机器人：你最喜欢的颜色是什么？
用户：银色

这些提示将被视为开放式终止，因为它们可以用任意值进行应答。

## <a name="what"></a>内容

### <a name="enum-entities"></a>枚举实体

创建的枚举实体与其他实体类似。 与 "编程" 实体类似，您无法将单词标记为这些实体。 相反，它们必须通过代码或 SET_ENTITY 操作进行设置。

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>设置实体操作

如上所述，"设置实体" 操作只是将实体设置为已知的枚举值。 通过创建 API 回调操作并使用内存管理器将实体设置为值，可以获得相同的结果。 例如 `memory.Set(entityName, entityValue)`。 必须编写此代码并创建这些操作，这会变得枯燥乏味且难以管理-因此 Conversation Learner 具有用于简化此工作并在使用它们时自动生成这些操作的特殊操作。 将这些操作作为独立的操作，可以保留这些功能，而不会与机器人中的其他操作或代码耦合在一起。

- 设置实体操作只能在引用枚举实体的值时创建，因此必须先创建枚举实体。
- 设置实体操作也是 "非等待" 的，因为它们没有可见的输出，需要由用户查看的 "等待" 操作跟随。
- 设置实体操作是不可变的，这意味着在创建后不能对其进行编辑。

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>自动操作生成

如果模型中存在枚举实体，Conversation Learner 将为每个可能的值创建占位符操作，并使它们可在定型期间选择。 选择后，将自动为你创建操作。

例如，如果我创建一个具有值 "Yes" 和 "No" 的枚举实体：

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

即使没有为此新枚举显式创建操作，也会在训练期间看到两个新操作：

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>使用这些新功能创建机器人

### <a name="requirements"></a>要求

本教程要求运行常规教程机器人

    npm run tutorial-general

我们会创建一个机器人来模拟快速食物排序。 对于饮料和薯条（小/中/大）的大小，它将具有离散值，并具有 "是/否" 答案的确认问题。 这两个实体都满足上述两个规则：上下文相关的答案和固定值。

### <a name="create-the-model"></a>创建模型

1. 在 web UI 中，单击 "导入"
2. 选择名为 "教程-枚举集-实体" 的教程

这会导航到 "模型管理" 页。
请注意，该模型已包含一些枚举实体并设置实体操作。

### <a name="create-the-first-dialog"></a>创建第一个对话

1. 在左侧导航面板中，单击 "训练对话框"，然后单击 "新建定型对话框" 按钮。
2. 用户键入 "你好，我想要定购 coke，薯条"。
3. 单击 "评分操作" 按钮

   > 用户未指定饮料或薯条的大小，因此我们需要询问他们。

4. 选择相应的操作："你喜欢什么大小饮料？"
5. 用户键入时，"大"
6. 单击 "评分操作" 按钮
7. 选择操作 SET_ENTITY-"drinkSize：大型
8. 选择相应的操作："你喜欢什么大小的薯条？"
9. 作为 "使用类型"，"Um，使其成为中型。
10. 单击 "评分操作" 按钮
11. 选择操作 SET_ENTITY-"friesSize：MEDIUM
12. 选择相应的操作："是否需要调味品？"
13. 作为使用类型，"是"
14. 单击 "评分操作" 按钮
15. 选择操作 SET_ENTITY-"condimentsConfirmation：
16. 选择相应的操作："好的，我有一个大饮料和中等薯条的订单。 正确吗？ "
17. 作为使用类型，"是"
18. 单击 "评分操作" 按钮
19. 选择操作 SET_ENTITY-"orderConfirmation：
20. 选择相应的操作："好的，你的订单号为58。 请稍候 ... "
21. 单击 "保存" 以关闭对话框

刚使用枚举实体和 SET_ENTITY 操作创建了第一个对话框。 您可以更多地组合用户指定部分信息或试用其他类型的封闭式问题。

> [!NOTE]
> 在训练过程中，你将看到 SET_ENTITY 操作的占位符，如
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> 但在创建日志对话框或使用已部署的 bot 时，用户将看不到这些。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [备用输入](./10-alternative-inputs.md)
