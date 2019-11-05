---
title: 如何：向自定义命令参数添加验证（预览）
titleSuffix: Azure Cognitive Services
description: 本文将验证添加到自定义命令参数
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 64e092405686caca7baeaf58f19d577a3f80e169
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506930"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>如何：向自定义命令参数添加验证（预览）

本文介绍如何将验证添加到参数和提示进行更正。

## <a name="prerequisites"></a>必备组件

你必须已完成以下文章中的步骤：

- [快速入门：创建自定义命令（预览）](./quickstart-custom-speech-commands-create-new.md)
- [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>创建 SetTemperature 命令

为了演示验证，让我们创建一个新的命令，让用户设置温度。

1. 在[Speech Studio](https://speech.microsoft.com/)中打开之前创建的自定义命令应用程序
1. 创建新的命令**SetTemperature**
1. 为目标温度添加参数
1. 为温度参数添加验证
   > [!div class="mx-imgBorder"]
   > ![添加范围验证](media/custom-speech-commands/validations-add-temperature.png)

   | 设置           | 建议的值                                          | 说明                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | 名称              | 温度                                              | 命令参数的描述性名称                                                    |
   | 需要          | true                                                     | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值 |
   | 响应模板 | "你喜欢哪个温度？"                       | 在未知情况下询问此参数值的提示                              |
   | 类型              | Number                                                   | 参数的类型，例如数字、字符串或日期时间                                      |
   | 验证        | 最小值：60，最大值：80                             | 对于 Number 参数，为参数允许的值范围                              |
   | 响应模板 | "很抱歉，只能在60到80度之间设置        | 如果验证失败，则提示要求提供更新的值                                       |

1. 添加一些示例句子

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 添加完成规则以确认结果

   | 设置    | 建议的值                                         | 说明                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | 规则名称  | 确认消息                                    | 描述规则用途的名称          |
   | 条件 | 必需参数-温度                        | 确定何时可以运行规则的条件    |
   | 操作    | SpeechResponse-"确定，设置为 {温度} 度" | 规则条件为 true 时要执行的操作 |

> [!TIP]
> 此示例使用语音响应来确认结果。 有关使用客户端操作完成命令的示例，请参阅：[如何：在客户端上使用语音 SDK 完成命令（预览）](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>试用

选择 "测试" 面板，尝试几个交互。

- 输入：将温度设置为72度
- 输出： "确定，设置为72度"

- 输入：将温度设置为45度
- 输出： "抱歉，只能在60到80度之间设置"
- 输入：改为72度
- 输出： "确定，设置为72度"
