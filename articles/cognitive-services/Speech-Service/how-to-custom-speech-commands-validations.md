---
title: 如何：向自定义命令参数添加验证
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将验证添加到自定义命令中的参数。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857194"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>如何：向自定义命令参数添加验证（预览）

本文介绍如何将验证添加到参数和提示进行更正。

## <a name="prerequisites"></a>必备条件

你必须已完成以下文章中的步骤：

> [!div class="checklist"]
> * [快速入门：创建自定义命令](./quickstart-custom-speech-commands-create-new.md)
> * [快速入门：使用参数创建自定义命令](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>创建 SetTemperature 命令

为了演示验证，让我们创建一个新的命令，让用户设置温度。

1. 在[Speech Studio](https://speech.microsoft.com/)中打开之前创建的自定义命令应用程序
1. 创建新命令`SetTemperature`
1. 为目标温度添加参数。

   | 参数配置           | 建议的值    |说明                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | 名称              | 温度                       | 参数的描述性名称                                |
   | 必选          | checked                           | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值 |
   | 响应所需的参数     | 简单编辑器-> 所需的温度？  | 在未知情况下询问此参数值的提示 |
   | 类型              | 数字                            | 参数的类型，例如 Number、String、Date Time 或 Geography   |

1. 添加温度参数的验证。

    - 在参数的**参数**配置页`Temperature`中，从`Add a validation` "验证" 部分中进行选择。
    - 按如下所示填写**新验证**弹出窗口中的值，然后选择 "**创建**"。

  
       | 参数配置         | 建议的值                                          | 说明                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | 最小值        | 60               | 对于 Number 参数，此参数可以采用的最小值 |
       | 最大值        | 80               | 对于 Number 参数，此参数可以采用的最大值 |
       | 故障响应-简单编辑器| 第一变体-抱歉，只能在60到80度之间设置      | 验证失败时提示要求输入新值                                       |

       > [!div class="mx-imgBorder"]
       > ![添加范围验证](media/custom-speech-commands/validations-add-temperature.png)

1. 添加一些示例句子

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 添加完成规则以确认结果

   | 设置    | 建议的值                                           |说明                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | 名称       | 确认消息                                      |描述规则用途的名称 |
   | 条件 | 必需的参数-`Temperature`                       |确定何时可以运行规则的条件    |   
   | 操作    | 发送语音响应-`Ok, setting temperature to {Temperature} degrees` | 规则条件为 true 时要执行的操作 |

> [!TIP]
> 此示例使用语音响应来确认结果。 有关使用客户端操作完成命令的示例，请参阅：[如何：在客户端上使用语音 SDK 完成命令](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>试试看
1. 选择`Train`位于右窗格顶部的图标。

1. 完成培训后，选择`Test`并尝试几个交互。

    - 输入：将温度设置为72度
    - 输出：正常，将温度设置为72度
    - 输入：将温度设置为45度
    - 输出：抱歉，只能在60到80度之间设置
    - 输入：改为72度
    - 输出：正常，将温度设置为72度

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：向自定义命令添加确认（预览版）](./how-to-custom-speech-commands-confirmations.md)
