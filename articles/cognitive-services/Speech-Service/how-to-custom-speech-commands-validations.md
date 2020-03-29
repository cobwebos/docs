---
title: 如何：将验证添加到自定义命令参数（预览）
titleSuffix: Azure Cognitive Services
description: 在本文中，我们将解释如何向自定义命令中的参数添加验证。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156448"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>如何：将验证添加到自定义命令参数（预览）

在本文中，您将学习如何向参数添加验证并提示更正。

## <a name="prerequisites"></a>先决条件

您必须已完成以下文章中的步骤：

- [快速入门：创建自定义命令（预览）](./quickstart-custom-speech-commands-create-new.md)
- [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>创建设置温度命令

为了演示验证，让我们创建一个新命令，允许用户设置温度。

1. 在[语音工作室](https://speech.microsoft.com/)中打开以前创建的自定义命令应用程序
1. 创建新的命令**集温度**
1. 添加目标温度的参数
1. 添加温度参数的验证
   > [!div class="mx-imgBorder"]
   > ![添加范围验证](media/custom-speech-commands/validations-add-temperature.png)

   | 设置           | 建议的值                                          | 描述                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | “属性”              | 温度                                              | 命令参数的描述性名称                                                    |
   | 必选          | true                                                     | 选中复选框，指示在完成命令之前是否需要此参数的值 |
   | 响应模板 | "-你想什么温度？"                     | 提示在不知道此参数时请求该参数的值                              |
   | 类型              | Number                                                   | 参数的类型，如数字、字符串或日期时间                                      |
   | 验证        | 最小值： 60， 最大值： 80                             | 对于 Number 参数，参数的允许值范围                             |
   | 响应模板 | "-对不起，我只能设置在60到80度之间"      | 如果验证失败，提示请求更新的值                                       |

1. 添加一些示例句子

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 添加完成规则以确认结果

   | 设置    | 建议的值                                           | 描述                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | 规则名称  | 确认消息                                      | 描述规则目的的名称          |
   | 条件 | 所需参数 - 温度                          | 确定规则何时可以运行的条件    |
   | 操作    | 语音响应 - "-确定，设置为 [温度] 度" | 规则条件为 true 时要执行的操作 |

> [!TIP]
> 此示例使用语音响应来确认结果。 有关使用客户端操作完成命令的示例，请参阅[：如何：使用语音 SDK 在客户端上执行命令（预览）](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>试用

选择"测试"面板并尝试一些交互。

- 输入：将温度设置为 72 度
- 输出："好的，设置为 72 度"

- 输入：将温度设置为 45 度
- 输出："对不起，我只能设置在60到80度之间"
- 输入：使其为 72 度
- 输出："好的，设置为 72 度"

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：向自定义命令添加确认（预览）](./how-to-custom-speech-commands-confirmations.md)
