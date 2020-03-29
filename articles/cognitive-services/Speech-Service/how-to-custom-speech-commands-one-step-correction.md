---
title: 如何：向自定义命令（预览）添加一步更正 - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，我们将解释如何在自定义命令中实现命令的一步更正。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456450"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>如何：向自定义命令添加一步更正（预览）

在本文中，您将学习如何向命令添加一步确认。

一步更正用于更新刚刚完成的命令。

即，如果你刚刚设置了报警器，你可以改变主意，更新闹钟的时间。

- 输入：为明天中午设置闹钟
- 输出："好的，2019年6月12日12：00：00的报警设置"
- 输入：否，明天下午 1 点
- 输出："好的

请记住，这意味着您作为开发人员有一个机制来更新后端应用程序中的警报。

## <a name="prerequisites"></a>先决条件

您必须已完成以下文章中的步骤：

- [快速入门：创建自定义命令（预览）](./quickstart-custom-speech-commands-create-new.md)
- [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
- [如何：向自定义命令添加确认（预览）](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>添加用于一步更正的高级规则 

为了演示一步更正，让我们扩展在["如何执行"](./how-to-custom-speech-commands-confirmations.md)中的**SetAlarm**命令。
 
1. 添加高级规则以更新上一个警报。 

    此规则将要求用户确认警报的日期和时间，并期待下一个回合的确认（是/否）。

   | 设置               | 建议的值                                                  | 描述                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 规则名称             | 更新上一个警报                                            | 描述规则目的的名称          |
   | 条件            | 更新状态命令&所需参数 - 日期时间                | 确定规则何时可以运行的条件    |   
   | 操作               | 语音响应 - "- 将上一个警报更新到 [DateTime]"       | 规则条件为 true 时要执行的操作 |
   | 执行后的状态 | 完成命令                                                 | 转弯后用户的状态                   |

1. 将刚刚创建的规则移到高级规则的顶部（滚动面板中的规则，然后单击 UP 箭头）。
   > [!div class="mx-imgBorder"]
   > ![添加范围验证](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> 在实际应用程序中，在此规则的"操作"部分中，您还将将活动发送回客户端或调用 HTTP 终结点以更新系统中的警报。

## <a name="try-it-out"></a>试用

选择"测试"面板并尝试一些交互。

- 输入：为明天中午设置闹钟
- 输出："是否确实要为 2019 年 7 月 12 日 12：00：00 设置闹钟？"
- 输入： 是
- 输出："好的，2019年7月12日12：00：00的报警设置"
- 输入：否，明天下午 1 点
- 输出："将以前的警报更新至 2019 年 7 月 12 日 13：00：00"
