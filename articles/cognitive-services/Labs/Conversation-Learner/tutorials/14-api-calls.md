---
title: 如何通过对话学习器模型使用 API 调用 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用 API 调用。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f7c7c72703d7c3134dd2acdcc466fc0182fa38a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389945"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>如何将 API 调用添加到对话学习器模型

本教程演示如何将 API 调用添加到模型。 API 调用是在机器人中定义和编写的函数，是 Conversation Learner 可以调用的函数。

## <a name="video"></a>视频

[![API 调用教程预览](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>要求
本教程要求运行“tutorialAPICalls.ts”机器人。

    npm run tutorial-api-calls

## <a name="details"></a>详细信息

- API 调用可以读取和操作实体。
- API 调用可以访问内存管理器对象。
- API 调用可以采用参数。

### <a name="open-the-demo"></a>打开演示

在 Web UI 中单击“导入教程”，选择名为“Tutorial-14-APICalls”的模型。

### <a name="entities"></a>实体

我们已在模型中定义了一个名为 `number` 的实体。

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API 调用
API 调用的代码在此文件中定义：`C:\<installedpath>\src\demos\tutorialAPICalls.ts`。

![](../media/tutorial12_apicalls.PNG)

- `RandomGreeting` 回调返回在 `greeting` 数组中定义的随机问候语。
- `Multiply` 回调会将由调用它的操作传入的两个数字相乘，并返回可以在 UI 中呈现的结果。
    - 请注意，内存管理器是第一个参数。 
    - 另请注意，API 回调可以采用多个输入（在此示例中为 `num1string` 和 `num2string`）。
- `ClearEntities` 回调会清除数字实体，以便用户能够输入另一个数字。 
    - 说明 API 调用如何才能操作实体。

### <a name="actions"></a>操作
我们已创建了四个操作。 其中三个操作为“非等待”API 操作，第四个操作为“文本”操作，该操作会询问用户一个问题，而该问题类似于我们在其他教程中看到过的问题。 若要了解每个操作是如何创建的，请执行以下操作：
1. 在左面板中单击“操作”，然后单击在网格中列出的四个操作中的一个。
2. 注意弹出的窗体中每个字段的值。
3. 注意 API 字段旁边的 `Refresh` 按钮。
    - 若要停止机器人并在 UI 页面显示时对 API 进行更改，则可单击 `Refresh` 按钮以选取最新更改。

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities、Multiply 和 RandomGreeting
这三个操作均为 API 类型。 它们都依赖于 API 回调功能来执行某些工作，并且可能会返回一个呈现给用户的值。

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>“你想要将什么数乘以 12?”
这是“文本”操作，它直接提问一个用户问题。 虽然此操作实际上不与其中的一个 API 回调交互，但却会提示用户使用一个数字进行响应。该数字会进入一个实体的内存中，然后该实体即可供使用某个 API 回调的“乘”操作使用。


### <a name="train-dialog"></a>训练对话

让我们完成一个“训练对话”。

1. 在左面板中单击“`Train Dialogs`”，然后单击“`New Train Dialog`”按钮。
2. 键入“你好”。
3. 单击“`Score Actions`”按钮。
4. 选择 `RandomGreeting`。 
    - 这将执行随机问候 API 调用。
    - 这不会等待用户响应。
5. 选择`What number to do you want to multiply by 12?`
6. 键入一个数字，该数字可以是任意数字，但只能是一个数字。
    - 注意，数字会被自动标记为 `number` 实体。
7. 单击“`Score Actions`”按钮。
8. 选择 `Multiply` 操作。
    - 注意乘以 12 后的结果。
    - 注意，内存仍包含为 `number` 输入的值
9. 选择 `ClearEntities` 操作。
    - 请注意，`number` 的实体值已从内存中清除。
10. 单击“`Save`”按钮。

你现在已了解了如何注册 API 回调、API 回调的常见模式，以及如何在其中定义参数并关联值和实体。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [卡片第 1 部分](./15-cards.md)
