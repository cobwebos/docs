---
title: 如何通过对话学习器应用程序使用 API 调用 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器应用程序使用 API 调用。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ec752cbadfac7a47e08ed7b0ffe8bb475969fac5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366279"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-application"></a>如何将 API 调用添加到对话学习器应用程序

本教程演示如何将 API 调用添加到应用程序。 API 调用是在机器人中定义和编写的函数，是对话学习器可以调用的函数。

## <a name="requirements"></a>要求
本教程要求运行“tutorialAPICalls.ts”机器人。

    npm run tutorial-api-calls

## <a name="details"></a>详细信息

- API 调用可以读取和操作实体。
- API 调用可以访问内存管理器对象。
- API 调用还可以使用参数 -- 这允许重复使用相同的 API 调用来实现不同的目的。

### <a name="open-the-demo"></a>打开演示

在 Web UI 的“应用”列表中，单击“Tutorial-12-APICalls”。 

### <a name="entities"></a>实体

我们已在应用程序中定义了一个名为 number 的实体。

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API 调用
API 调用的代码在此文件中定义：C:\<installedpath\>\src\demos\tutorialAPICalls.ts。

![](../media/tutorial12_apicalls.PNG)

- 第一个 API 回调是 RandomGreeting。 它返回 greeting 变量中定义的随机问候语。
- 乘法 API 回调：它将把用户提供的两个数字相乘。 然后返回两个数字相乘的结果。 这表明 API 回调可以接受输入。 请注意，内存管理器是第一个参数。 
- ClearEntities API 回调：清除数字实体以允许用户输入下一个数字。 这说明了 API 调用如何操作实体。

### <a name="actions"></a>操作

我们已创建了四个操作。 

![](../media/tutorial12_actions.PNG)

- 除了“What number do you want to multiply by 12?” （这是一种交流行为）外，还有三种不同的 API 调用可以说明典型的 API 调用模式。

- RandomGreeting：是一个非等待操作。 为了设置此项，我们在“创建操作”对话中，选择了 API_LOCAL 操作类型，然后选择了 RandomGreeting。 

![](../media/tutorial12_setupapicall.PNG)

如果我们要停止机器人并对 API 进行任何更改，则使用 API 旁边的“刷新”按钮。 单击“刷新”将选取最新的更改。

以下是我们创建 Multiply 操作的方法：选择 API_Local 和 API 后，我们为第一个输入值 (num1string) 输入了一个实体 ($number)，为第二个输入值 (num2string) 输入了一个值 (12)。 这提供了机器人和 API 调用之间的间接级别，因此可以将同一个回调映射到系统中的一些操作，并且它们在如何分配操作方面有所不同。

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>训练对话

让我们完成一个教学对话。

1. 依次单击“训练对话”和“新建训练对话”。
1. 输入“hi”。
2. 单击“对操作打分”。
3. 单击以选择“RandomGreeting”。 这将执行随机问候 API 调用。
3. 单击以选择“What number to do you want to multiply by 12?”
4. 输入“8”。 然后单击“对操作打分”。
4. 选择“Multiply $number 12”。 请注意乘法的结果。
5. 选择“清除实体”。
    - 请注意，已清除数字实体的值。
3. 单击以选择“What number to do you want to multiply by 12?”
4. 单击“完成测试”。

![](../media/tutorial12_dialog.PNG)

你现在已了解了如何注册 API 回调、API 回调的常见模式，以及如何在其中定义参数并关联值和实体。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [卡片第 1 部分](./13-cards-1.md)
