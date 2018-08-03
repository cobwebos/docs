---
title: 演示对话学习器模型，披萨订购 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何创建演示对话学习器模型。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 052ef249f3367a562e5598b90533c0e52ed75df4
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171378"
---
# <a name="demo-pizza-order"></a>演示：披萨订购
此演示演示了披萨订购机器人。 它支持使用此功能订购单个披萨：

- 在用户陈述中识别比萨配料
- 检查比萨配料是有库存还是缺货，并做出适当的响应
- 记住之前订单上的披萨配料，并提供用相同的配料开始新订单

## <a name="video"></a>视频

[![演示披萨预览](http://aka.ms/cl-demo-pizza-preview)](http://aka.ms/blis-demo-pizza)

## <a name="requirements"></a>要求
本教程要求运行披萨订购机器人

    npm run demo-pizza

### <a name="open-the-demo"></a>打开演示

在 Web UI 的“模型”列表中，单击“TutorialDemo Pizza Order”。 

## <a name="entities"></a>实体

已创建三个实体。

- Toppings：此实体将累积用户要求的配料。 它包括有库存的有效配料。 它会检查配料是有库存还是缺货。
- OutofStock：此实体用来告诉用户他们选择的配料没有存货。
- LastToppings：下订单后，此实体用于向用户提供其订单上的配料列表。

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>操作

你已经创建了一组操作，包括询问用户他们想要在披萨上加什么配料，告诉用户到目前为止他们已添加了什么，等等。

此外，还有两个 API 调用：

- FinalizeOrder：用于下披萨订单
- UseLastToppings：用于从先前的订单迁移配料 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>定型对话
你已经定义了少量定型对话。 

![](../media/tutorial_pizza_dialogs.PNG)

例如，让我们尝试一个教学会话。

1. 依次单击“训练对话”和“新建训练对话”。
1. 输入“order a pizza”。
2. 单击“对操作打分”。
3. 单击以选择“what would you like on your pizza?”
4. 输入“mushrooms and cheese”。
    - 请注意 LUIS 已将这两者都标记为 Toppings。 如果这不正确，你可以单击使其突出显示，然后进行更正。
    - 实体旁边的“+”号表示它将添加到配料组中。
5. 单击“对操作打分”。
    - 注意 `mushrooms` 和 `cheese` 不在 Toppings 的内存中。
3. 单击以选择“you have $Toppings on your pizza”
    - 注意，这是一个非等待操作，因此机器人会请求下一个操作。
6. 选择“Would you like anything else?”
7. 输入“remove mushrooms and add peppers”。
    - 注意 `mushroom` 旁边有一个“-”号表示要将其删除。 `peppers` 旁边有一个“+”号表示要将其添加到配料中。
2. 单击“对操作打分”。
    - 注意 `peppers` 现在是粗体，因为它是新加的。 并且 `mushrooms` 已被删掉。
8. 单击以选择“you have $Toppings on your pizza”
6. 选择“Would you like anything else?”
7. 输入“add peas”。
    - `Peas` 是缺货的配料的示例。 它仍被标记为配料。
2. 单击“对操作打分”。
    - `Peas` 显示为 OutOfStock。
    - 要查看这是如何实现的，请打开 `C:\<\installedpath>\src\demos\demoPizzaOrder.ts` 处的代码。 查看 EntityDetectionCallback 方法。 此方法在每个配料后调用，看它是否有库存。 如果没有，此方法会从配料集中清除该配料并将该配料添加到 OutOfStock 实体。 inStock 变量在该方法的上方定义，其中包含有库存的配料列表。
6. 选择“We don't have $OutOfStock”。
7. 选择“Would you like anything else?”
8. 输入“no”。
9. 单击“对操作打分”。
10. 选择“FinalizeOrder”API 调用。 
    - 这将调用代码中定义的“FinalizeOrder”函数。 这清除配料，并返回“your order is on its way”。 
2. 输入“order another”。 我们正在启动新订单。
9. 单击“对操作打分”。
    - “cheese”和“pepper”在内存中作为上一个订单的配料。
1. 选择“Would you like $LastToppings”。
2. 输入“yes”
3. 单击“对操作打分”。
    - 机器人想要执行 UseLastToppings 操作。 该操作是两个回调方法中的第二个。 它将把上一个订单的配料复制到 Toppings 中，并清除上一个 Toppings。 这是记住上一个订单的一种方式，如果用户说他们想要另一个披萨，则提供这些配料作为选项。
2. 单击以选择“you have $Toppings on your pizza”。
3. 选择“Would you like anything else?”
8. 输入“no”。
4. 单击“完成教学”。

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [演示 - VR 应用启动器](./demo-vr-app-launcher.md)
