---
title: 如何：向自定义命令（预览）添加单步更正-语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在自定义命令中为命令实现单步更正。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456450"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>如何：向自定义命令添加单步更正（预览）

本文介绍如何向命令添加单步确认。

单步更正用于更新刚完成的命令。

也就是说，如果只是设置了警报，则可以改变主意并更新警报时间。

- 输入：为明天中午设置警报
- 输出： "正常，为 12/06/2019 12:00:00 设置的警报"
- 输入：否，明天下午
- 输出： "正常

请记住，这意味着，作为开发人员，你可以在后端应用程序中使用一种机制来更新警报。

## <a name="prerequisites"></a>必备组件

你必须已完成以下文章中的步骤：

- [快速入门：创建自定义命令（预览）](./quickstart-custom-speech-commands-create-new.md)
- [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
- [如何：向自定义命令添加确认（预览版）](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>为单步更正添加高级规则 

若要演示单步更正，请扩展在[确认如何](./how-to-custom-speech-commands-confirmations.md)操作中创建的**SetAlarm**命令。
 
1. 添加高级规则以更新以前的警报。 

    此规则将要求用户确认警报的日期和时间，并在下一轮时间要求确认（是/否）。

   | 设置               | 建议的值                                                  | Description                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 规则名称             | 更新上一个警报                                            | 描述规则用途的名称          |
   | 条件            | UpdateLastCommand & 必需的参数-DateTime                | 确定何时可以运行规则的条件    |   
   | 操作               | SpeechResponse-"-将以前的警报更新到 {DateTime}"       | 规则条件为 true 时要执行的操作 |
   | 执行后的状态 | 完成命令                                                 | 转换后的用户状态                   |

1. 将刚刚创建的规则移动到高级规则的顶部（在面板中滚动规则并单击向上箭头）。
   > [!div class="mx-imgBorder"]
   > ![添加范围验证](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> 在实际应用程序中，在此规则的 "操作" 部分，还会将活动发送回客户端，或调用 HTTP 终结点以更新系统中的警报。

## <a name="try-it-out"></a>试试看

选择 "测试" 面板，尝试几个交互。

- 输入：为明天中午设置警报
- 输出： "是否确实要为 12/07/2019 12:00:00 设置警报？"
- 输入：是
- 输出： "正常，为 12/07/2019 12:00:00 设置的警报"
- 输入：否，明天下午
- 输出： "将以前的警报更新为 12/07/2019 13:00:00"
