---
title: 在自定义命令中添加单步更正预览-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在自定义命令预览应用中添加命令的单步更正。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310421"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>在自定义命令预览应用程序中添加自定义命令的单步更正

在本文中，你将了解如何在自定义命令预览应用程序中将单步确认添加到命令。

单步更正用于更新刚完成的命令。 向警报命令添加单步更正时，可以改变主意并更新警报时间。 例如：

- 输入：为明天中午设置警报
- 输出：正常，为 2020-05-02 12:00:00 设置的警报
- 输入：否，明天下午
- 输出：正常

> [!NOTE]
> 在实际方案中，客户端通过执行命令来执行操作。 本文假设你有一种机制，用于在后端应用程序中更新警报。

## <a name="prerequisites"></a>先决条件

完成以下文章中的步骤：
> [!div class="checklist"]

> * [快速入门：创建自定义命令预览应用](./quickstart-custom-speech-commands-create-new.md)
> * [快速入门：使用参数创建自定义命令预览应用](./quickstart-custom-speech-commands-create-parameters.md)
> * [如何：在自定义命令预览应用中将确认添加到命令](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>为单步更正添加交互规则

若要演示单步更正，请扩展在[如何：在自定义命令预览中添加对命令的确认](./how-to-custom-speech-commands-confirmations.md)中创建的**SetAlarm**命令。

1. 添加交互规则以更新**SetAlarm**命令。

    此规则要求用户确认警报的日期和时间，并在下一轮需要确认（是/否）。

   | 设置               | 建议的值                                                  | 描述                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **规则名称**             | **更新上一个警报**                                            | 描述规则用途的名称          |
   | **条件**            | **需要更新上一个命令 & 必需的参数-> DateTime**                | 确定何时可以运行规则的条件    |   
   | **操作**               | **发送语音响应-> 简单编辑器-将以前的警报更新为 {DateTime} >**      | 规则条件为 true 时要执行的操作 |
   | **执行后状态** | **命令已完成**        | 转换后的用户状态                   |

1. 在窗格中，选择刚创建的交互规则。 选择窗格左上角的**省略号（"..."**）按钮。 然后，使用 "**上移**" 箭头将规则移到 "**交互规则**" 列表的顶部。
   > [!div class="mx-imgBorder"]
   > ![添加范围验证](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > 在实际应用程序中，使用 "**操作**" 部分将活动发送回客户端，或调用 HTTP 终结点以更新系统中的警报。

## <a name="try-it-out"></a>试试看

1. 选择“训练”。

1. 完成训练后，选择 "**测试**"，然后尝试这些交互：

   - 输入：为明天中午设置警报
   - 输出：是否确实要为 2020-05-02 12:00:00 设置警报
   - 输入：是
   - 输出：正常，为 2020-05-02 12:00:00 设置的警报
   - 输入：否，明天下午2：
   - 输出：将以前的警报更新为 2020-05-02 14:00:00
