---
title: 如何：向自定义命令添加确认（预览版）
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在自定义命令中实现命令的确认。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456494"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>如何：向自定义命令添加确认（预览版）

在本文中，你将学习如何向命令添加确认。

## <a name="prerequisites"></a>必备组件

你必须已完成以下文章中的步骤：

- [快速入门：创建自定义命令（预览）](./quickstart-custom-speech-commands-create-new.md)
- [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>创建 SetAlarm 命令

为了演示验证，让我们创建一个新的命令，让用户设置警报。

1. 在[Speech Studio](https://speech.microsoft.com/)中打开之前创建的自定义命令应用程序
1. 创建新的命令**SetAlarm**
1. 添加一个名为 DateTime 的参数

   | 设置           | 建议的值                                          | Description                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | 名称              | 日期/时间                                                 | 命令参数的描述性名称                                                    |
   | 需要          | true                                                     | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值 |
   | 响应模板 | "-什么时间？"                                           | 在未知情况下询问此参数值的提示                              |
   | 类型              | 日期/时间                                                 | 参数的类型，例如数字、字符串或日期时间                                      |
   | 日期默认值     | 如果当前缺少日期，请使用                             |                                                                                                  |
   | 时间默认值     | 如果缺少时间，则使用 "日开始"                      |                                                                                                  | 

1. 添加一些示例句子
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. 添加完成规则以确认结果

   | 设置    | 建议的值                                         | Description                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | 规则名称  | 设置警报                                               | 描述规则用途的名称          |
   | 操作    | SpeechResponse-"-正常，为 {DateTime} 设置警报"       | 规则条件为 true 时要执行的操作 |

## <a name="try-it-out"></a>试试看

选择 "测试" 面板，尝试几个交互。

- 输入：为明天中午设置警报
- 输出： "正常，为 12/06/2019 12:00:00 设置的警报"

- 输入：设置警报
- 输出： "什么时间？"
- 输入：下午
- 输出： "正常，为 12/05/2019 17:00:00 设置的警报"

## <a name="add-the-advanced-rules-for-confirmation"></a>添加用于确认的高级规则

1. 添加用于确认的高级规则。 

    此规则将要求用户确认警报的日期和时间，并在下一轮时间要求确认（是/否）。

   | 设置               | 建议的值                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | 规则名称             | 确认日期时间                                                                | 描述规则用途的名称          |
   | 条件            | 必需的参数-日期时间                                                    | 确定何时可以运行规则的条件    |   
   | 操作               | SpeechResponse-"-是否确实要为 {DateTime} 设置警报？"       | 规则条件为 true 时要执行的操作 |
   | 执行后的状态 | 等待输入                                                                   | 转换后用户的状态                  |
   | 一致          | 确认                                                                     | 预期下一轮                      |

1. 添加高级规则以处理成功的确认（用户说 "是"）

   | 设置               | 建议的值                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | 规则名称             | 接受的确认                                                            | 描述规则用途的名称          |
   | 条件            | SuccessfulConfirmation & 必需的参数-DateTime                           | 确定何时可以运行规则的条件    |   
   | 执行后的状态 | 准备完成                                                             | 转换后的用户状态                   |

1. 添加高级规则以处理已拒绝的确认（用户未说）

   | 设置               | 建议的值                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | 规则名称             | 已拒绝确认                                                                   | 描述规则用途的名称          |
   | 条件            | DeniedConfirmation & 必需的参数-DateTime                               | 确定何时可以运行规则的条件    |   
   | 操作               | ClearParameter & SpeechResponse-"-没有问题，那是什么时间？"     | 规则条件为 true 时要执行的操作 |
   | 执行后的状态 | 等待输入                                                                   | 转换后的用户状态                   |
   | 一致          | ElicitParameters-DateTime                                                      | 预期下一轮                      |

## <a name="try-it-out"></a>试试看

选择 "测试" 面板，尝试几个交互。

- 输入：为明天中午设置警报
- 输出： "是否确实要为 12/07/2019 12:00:00 设置警报？"
- 输入：否
- 输出： "没有问题，那是什么时间？"
- 输入：下午
- 输出： "是否确实要为 12/06/2019 17:00:00 设置警报？"
- 输入：是
- 输出： "正常，为 12/06/2019 17:00:00 设置的警报"

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：向自定义命令添加单步更正（预览）](./how-to-custom-speech-commands-one-step-correction.md)