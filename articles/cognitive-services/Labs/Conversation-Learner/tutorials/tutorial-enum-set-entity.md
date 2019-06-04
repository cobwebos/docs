---
title: 何时使用枚举实体和设置实体操作使用对话学习器模型-Azure 认知服务 |Microsoft Docs
titleSuffix: Azure
description: 了解何时适合使用对话学习器模型中使用枚举实体和设置实体操作。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1393d0bd1c31a2c9c24652e260ef7f3182d91367
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476490"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>何时使用枚举的实体和设置实体操作

本教程将介绍何时应该使用枚举 （枚举） 实体和 SET_ENTITY 操作。

## <a name="video"></a>视频

[![设置实体教程预览](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>涵盖的内容

本教程将介绍两项新功能。 新的实体类型称为枚举 （的缩写枚举），新类型的操作称为 SET_ENTITY，这些枚举值之一，可以参考，如名称所示，将实体设置为此值。 您将了解下面，这些新功能一起使用，我们将说明以及如何使用它们下面。 在我们开始了解详细信息之前，请务必了解这些功能可帮助解决哪些问题。

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>问题

在其中的单词的含义取决于上下文的对话中有些情况。  通常标记为的关键字学习，并提取使用语言理解服务，但在这些情况下这些系统可能不能以了解使用的标记的示例。

假设 overhear 附近的人员之间的会话的一部分，您只能听到单词"yes。" 你将无法判断""同意或确认，因为您没有听到这个问题之前要求。 之前问的问题是上下文，它提供答案的含义。 同样自"是"是对许多不同的问题，它不能使用所提供的示例介绍了此类的公共响应[自定义已训练](04-introduction-to-entities.md)实体因为然后它会了解每个"是"作为该实体标记。

### <a name="example"></a>示例

让我们进一步阐明与下面的示例：

智能机器人应用程序：您是否喜欢 Azure 认知服务？
用户：是的智能机器人应用程序：您是否喜欢 ice cream？
用户：是

在前面的教程中，我们会学会[自定义已训练](04-introduction-to-entities.md)实体，并且在初始认为可能是可以创建名为"likesCogServices"实体并第一个是"Yes"作为此实体标记。  但是，系统还将"是 Yes"标签第二个。 当我们尝试更正"是"以"likesIceCream"第二个标签时，我们会再产生冲突的两个相同的输入"Yes"含义不同的操作，并总是会。

它是在这些情况下，您需要使用枚举的实体和 SET_ENTITY 操作。

## <a name="when-to-use-enums-or-setentity-actions"></a>何时使用枚举或 SET_ENTITY 操作

使用以下这些规则来了解何时使用枚举的实体和 SET_ENTITY 操作：

- 检测或实体的设置与上下文相关
- 固定数量的可能值 （是，不会为两个值）

换而言之，将其用于任何封闭式如始终导致 Yes 或 no。 确认问题提示

> [!NOTE]
> 我们当前提供的每个枚举实体最多 5 个值的限制。 每个值使用当前 64 限制中的槽之一。 请参阅[cl 值和边界](../cl-values-and-boundaries.md)

示例：智能机器人应用程序：是你的订单吗？
用户：是

开放和不固定的实体的可能的值时，需要使用替代功能如下所示[预期实体](05-expected-entity.md)。

示例：智能机器人应用程序：你叫什么名字？
用户：Matt 智能机器人应用程序：什么是您喜爱的颜色？
用户：银牌服务

这些提示被视为无终止，因为它们无法找到相应的任意值。

## <a name="what"></a>对象

### <a name="enum-entities"></a>枚举实体

就像其他实体一样创建枚举实体。 类似于"编程"的实体，不能为这些实体标记的单词。 相反，它们必须通过代码或 SET_ENTITY 操作设置。

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>设置实体的操作

如上所述，"设置实体"操作只需设置实体为已知的枚举值。 通过创建 API 回调操作并使用内存管理器来将实体设置为一个值，可获得相同的结果。 例如 `memory.Set(entityName, entityValue)`。 无需编写此代码和创建这些操作会变得枯燥乏味且难以管理-因此对话学习器具有特殊操作来促进这项工作，并自动生成这些操作中使用时。 将这些功能作为独立操作保留的功能来编写这些而无需进行耦合与其他操作或在机器人中的代码。

- 因此，必须首先创建枚举实体引用的枚举实体值时，只能创建设置实体的操作。
- 设置实体的操作是还"非 await"由于任何可见输出，并且需要跟在用户可以看到"等待"操作。
- 设置实体的操作是不可变的这意味着不能在创建后编辑。

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>自动操作生成

如果某个枚举实体存在于您的模型，对话学习器将创建针对每个可能的值的操作的占位符，并使其可用于在定型期间选择。 选择它后操作自动将为你创建。

例如，如果我创建了枚举实体的值"Yes"和"No":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

即使没有显式创建此新枚举操作中，您将看到在定型过程中可用的两个新操作：

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>创建使用这些新功能的智能机器人

### <a name="requirements"></a>要求

本教程要求运行常规教程机器人

    npm run tutorial-general

我们将创建智能机器人应用程序，以模拟快餐排序。 它将具有离散值饮料和薯条 (SMALL/MEDIUM/LARGE) 的大小和确认问题是与 / 没有答案。 这些实体的两个满足以上两个规则的依赖于上下文的答案和固定的值。

### <a name="create-the-model"></a>创建模型

1. 在 web UI 中，单击"导入"
2. 选择名为"教程-枚举-集-实体"教程

这会将您导航到模型管理页面。
请注意，该模型已包含几个枚举实体并设置实体的操作。

### <a name="create-the-first-dialog"></a>创建第一个对话

1. 在左侧的导航面板中，单击"训练对话框"，然后单击"新的训练对话框"按钮。
2. 中的用户类型，作为"大家好，我想要请订单 coke 和薯条"。
3. 单击"评分操作"按钮

   > 用户未指定饮料的大小或薯条，因此我们需要让他们。

4. 选择包含响应的操作："什么大小饮料？"
5. 作为中，"large"的用户类型
6. 单击"评分操作"按钮
7. 选择操作 SET_ENTITY-"drinkSize:大型"
8. 选择包含响应的操作："有多大薯条是否？"
9. 作为使用类型中，"嗯，完成这些步骤。
10. 单击"评分操作"按钮
11. 选择操作 SET_ENTITY-"friesSize:MEDIUM"
12. 选择包含响应的操作："你希望任何调味品？"
13. 在中，"是"使用类型为
14. 单击"评分操作"按钮
15. 选择操作 SET_ENTITY-"condimentsConfirmation:是"
16. 选择包含响应的操作："好了，我有大型饮料和中等薯条的顺序。 是这样吗？"
17. 在中，"是"使用类型为
18. 单击"评分操作"按钮
19. 选择操作 SET_ENTITY-"orderConfirmation:是"
20. 选择包含响应的操作："好了，您的订单号是 58。 请等待那边。"
21. 单击"保存"以关闭对话框

您刚创建使用枚举实体和 SET_ENTITY 操作在第一个对话框。 您可以进行更多的组合的用户指定部分信息或与其他类型的封闭式问题进行试验。

> [!NOTE]
> 在训练期间你将看到占位符 SET_ENTITY 操作如
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> 但对话框或使用部署创建日志时智能机器人用户将看到这些。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [备用输入](./10-alternative-inputs.md)
