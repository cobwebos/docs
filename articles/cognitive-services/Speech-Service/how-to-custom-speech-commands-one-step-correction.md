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
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858268"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>如何：向自定义命令添加单步更正（预览）

本文介绍如何向命令添加单步确认。

单步更正用于更新刚完成的命令。 也就是说，如果只是设置了警报，则可以改变主意并更新警报时间。 此类情况的示例如下所示：

- 输入：为明天中午设置警报
- 输出：正常，为 2020-05-02 12:00:00 设置的警报
- 输入：否，明天下午
- 输出：正常

一个真实的方案，该方案通常伴随着客户端执行操作作为命令完成的结果-本文假定你作为开发人员，可以在后端应用程序中更新警报。

## <a name="prerequisites"></a>必备条件

你必须已完成以下文章中的步骤：
> [!div class="checklist"]

> * [快速入门：创建自定义命令（预览）](./quickstart-custom-speech-commands-create-new.md)
> * [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
> * [如何：向自定义命令添加确认（预览版）](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>为单步更正添加交互规则 

为了演示单步更正，我们扩展了[如何：向自定义命令添加确认（预览版）](./how-to-custom-speech-commands-confirmations.md)中创建的**SetAlarm**命令。
1. 添加交互规则以更新以前设置的警报。 

    此规则将要求用户确认警报的日期和时间，并在下一轮时间要求确认（是/否）。

   | 设置               | 建议的值                                                  | 说明                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 规则名称             | 更新上一个警报                                            | 描述规则用途的名称          |
   | 条件            | 需要更新上一个命令 & 必需的参数-> DateTime                | 确定何时可以运行规则的条件    |   
   | 操作               | 发送语音响应-> 简单编辑器-将以前的警报更新为 {DateTime} >      | 规则条件为 true 时要执行的操作 |
   | 执行后状态 | 命令已完成        | 转换后的用户状态                   |

1. 将刚刚创建的规则移动到交互规则的顶部（在面板中选择规则，并单击中间窗格顶部图标下`...`的向上箭头）。
   > [!div class="mx-imgBorder"]
   > ![添加范围验证](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > 在实际应用程序中，在此规则的 "操作" 部分，还会将活动发送回客户端，或调用 HTTP 终结点以更新系统中的警报。

## <a name="try-it-out"></a>试试看

选择`Train`，等待训练完成，并选择`Test`。

- 输入：为明天中午设置警报
- 输出：是否确实要为 2020-05-02 12:00:00 设置警报
- 输入：是
- 输出：正常，为 2020-05-02 12:00:00 设置的警报
- 输入：否，明天下午2：
- 输出：将以前的警报更新为 2020-05-02 14:00:00
