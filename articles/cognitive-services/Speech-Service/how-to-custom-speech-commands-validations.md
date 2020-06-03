---
title: 在自定义命令中添加验证预览-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在自定义命令预览应用程序中将验证添加到命令参数。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 6686016f109fad4ee8b7f4e494b1374a6003658c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310404"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>向自定义命令预览应用程序中的命令参数添加验证

在本文中，你将学习如何将验证添加到参数和提示进行更正。

## <a name="prerequisites"></a>先决条件

完成以下文章中的步骤：

> [!div class="checklist"]
 
> * [快速入门：创建自定义命令预览应用](./quickstart-custom-speech-commands-create-new.md)
> * [快速入门：使用参数创建自定义命令预览应用](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>创建 SetTemperature 命令

若要演示验证，请创建允许用户设置温度的新命令。

1. 在[Speech Studio](https://speech.microsoft.com/)中，打开您创建的自定义命令预览应用程序。
1. 创建新的**SetTemperature**命令。
1. 添加具有以下配置的温度参数：

   | 参数配置           | 建议的值    |描述                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **名称**              | **温度**                       | 参数的描述性名称                                |
   | **必需**          | 已选中                           | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值 |
   | **响应所需的参数**     | **简单编辑器-> 所需的温度？**  | 在未知情况下询问此参数值的提示 |
   | **类型**              | **数字**                            | 参数的类型，如 Number、String、DateTime 或 Geography   |

1. 添加温度参数的验证。

    1. 在温度参数的 "**参数**" 配置页中，选择 **"验证" 部分中**的 "**添加验证**"。

    1. 在新的 "**验证**" 弹出窗口中，按如下所示配置验证：
  
       | 参数配置         | 建议的值                                          | 描述                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **最小值**        | **60**               | 对于 Number 参数，此参数可以采用的最小值 |
       | **最大值**        | **80**               | 对于 Number 参数，此参数可以采用的最大值 |
       | **故障响应-简单编辑器**| **第一变体-抱歉，只能在60到80度之间设置**      | 验证失败时提示要求输入新值                                       |

       > [!div class="mx-imgBorder"]
       > ![添加范围验证](media/custom-speech-commands/validations-add-temperature.png)

1. 选择“创建”。

1. 添加一些示例句子。

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 添加具有以下配置的完成规则。 此规则确认结果。

   | 设置    | 建议的值                                           |说明                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | 名称       | 确认消息                                      |描述规则用途的名称 |
   | **条件** | **必需参数-温度**                       |确定何时可以运行规则的条件    |   
   | **操作**    | **发送语音响应-确定，将温度设置为 {温度} 度** | 规则条件为 true 时要执行的操作 |

> [!TIP]
> 此示例使用语音响应来确认结果。 有关使用客户端操作完成命令的示例，请参阅[如何：在客户端上使用语音 SDK 完成命令](./how-to-custom-speech-commands-fulfill-sdk.md)。

## <a name="try-it-out"></a>试试看

1. 选择“训练”。

1. 完成训练后，选择 "**测试**"，然后尝试这些交互：

    - 输入：将温度设置为72度
    - 输出：正常，将温度设置为72度
    - 输入：将温度设置为45度
    - 输出：抱歉，只能在60到80度之间设置
    - 输入：改为72度
    - 输出：正常，将温度设置为72度

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将确认添加到自定义命令预览应用中的命令](./how-to-custom-speech-commands-confirmations.md)
