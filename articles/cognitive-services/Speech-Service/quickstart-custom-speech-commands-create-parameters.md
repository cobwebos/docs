---
title: 快速入门：使用参数创建自定义命令预览应用-语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，你将向自定义命令应用程序添加参数，以便它可以打开和关闭多个设备。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 5461ef9680ab89c8cc9cc2e1166366abb04a6eab
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142276"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>快速入门：使用参数创建自定义命令预览应用程序

在[前面的文章](./quickstart-custom-speech-commands-create-new.md)中，已创建了一个没有参数的简单自定义命令应用程序。

在本文中，你将利用参数扩展该应用程序，以便它可以打开和关闭多个设备。

## <a name="create-parameters"></a>创建参数

1. 打开在[前面的文章](./quickstart-custom-speech-commands-create-new.md)中创建的项目。

   我们将编辑现有的命令，使其可用于打开和关闭多个设备。
1. 因为该命令现在同时处理 on 和 off，请将其重命名为**TurnOnOff**。
   1. 在左窗格中，选择 " **TurnOn** " 命令，然后在窗格顶部的 "**新建命令**" 旁选择**省略号（"..."**）按钮。
   
   1. 选择 "**重命名**"。 在 "**重命名" 命令**窗口中，将**名称**更改为**TurOnOff**。 选择“保存” 。

1. 创建一个参数，用于表示用户是要打开还是关闭设备。
   1. 选择中间窗格顶部的 "**添加**"。 在下拉列表中，选择 "**参数**"。
   1. 在右窗格的 "**参数**" 部分中，在 "**名称**" 框中添加一个值。
   1. 选择 "**必需**"。 在 "**为所需参数添加响应**" 窗口中，选择 "**简单编辑器**"。 在**第一个变体**框中，输入以下文本：
        ```
        On or Off?
        ```
   1. 选择“更新”。

       > [!div class="mx-imgBorder"]
       > ![创建所需的参数响应](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. 按如下所示配置参数的其余属性：
       

    | 配置      | 建议的值     | 说明                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **名称**               | **麦克风**           | 参数的描述性名称。                                                                  |
    | **为全局**          | 已清除       | 一个复选框，该复选框指示参数的值是否全局应用于应用程序中的所有命令。|
    | **必需**           | 已选定         | 一个复选框，该复选框指示是否需要参数的值。  |
    | **响应所需的参数**      |**简单编辑器-> 打开或关闭？**      | 当未知时询问参数值的提示。 |
    | **类型**               | **字符串**          | 参数的类型。 例如，Number、String、Date Time、Geography。   |
    | **配置**      | **接受来自内部目录的预定义输入值** | 对于字符串，此设置将输入限制为一组可能值。 |
    | **预定义的输入值**     | **打开**、**关闭**             | 一组可能的值及其别名。         |
       


    > [!div class="mx-imgBorder"]
    > ![Create 参数](media/custom-speech-commands/create-on-off-parameter.png)

1. 选择“保存”****，保存这些设置。

 1. 再次选择 "**添加**" 以添加另一个参数。 此参数表示设备的名称。 使用以下设置：
   

       | 设置            | 建议的值       | 说明                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **名称**               | **SubjectDevice**         | 参数的描述性名称。                                                                     |
       | **为全局**          | 已清除             | 一个复选框，该复选框指示参数的值是否全局应用于应用程序中的所有命令。 |
       | **必需**           | 已选定               | 一个复选框，该复选框指示是否需要参数的值。          |
       | **简单编辑器**      | **哪个设备？**    | 当未知时询问参数值的提示。                                       |
       | **类型**               | **字符串**                | 参数的类型。 例如，Number、String、Date Time、Geography。                                                |
       | **配置**      | **接受来自内部目录的预定义输入值** | 对于字符串，此设置将输入限制为一组可能值。       |
       | **预定义的输入值** | **电视**、**风扇**               | 一组可能的值及其别名。                               |
       | **别名**（电视）      | **电视**， **telly**     | 每个预定义输入值的可选别名。                                 |

## <a name="add-example-sentences"></a>添加示例句子

对于具有参数的命令，添加涵盖所有可能组合的示例句子很有用。 例如：

- 完整参数信息：`turn {OnOff} the {SubjectDevice}`
- 部分参数信息：`turn it {OnOff}`
- 无参数信息：`turn something`

包含不同信息量的示例句子允许自定义命令应用程序解析包含部分信息的一次性分辨率和多轮分辨率。

明确这一点后，编辑示例句子以使用此处建议的参数：

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> 在示例句子编辑器中，使用大括号引用参数： `turn {OnOff} the {SubjectDevice}` 。
>
> 使用选项卡自动完成由以前创建的参数定义。

## <a name="add-parameters-to-completion-rules"></a>向完成规则添加参数

修改在[上一个快速入门](./quickstart-custom-speech-commands-create-new.md)中创建的完成规则。

1. 在 "**条件**" 部分中，选择 "**添加条件**"。
1. 在 "**新建条件**" 窗口的 "**类型**" 列表中，选择 "**必需参数**"。 在列表中，选择 "**麦克风**" 和 " **SubjectDevice**"。
1. 选择“创建” 。
1. 在 "**操作**" 部分中，通过将鼠标悬停在操作上并选择 "编辑" 按钮来编辑现有的 "**发送语音响应**" 操作。 这一次，请使用新的 `OnOff` 和 `SubjectDevice` 参数：

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>试试看
1. 选择右窗格顶部的 "**定型**"。

1. 完成训练后，选择 "**测试**"。
    
    **您的应用程序**窗口将出现。

1. 尝试几个交互。

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：将自定义命令用于自定义语音（预览）](./quickstart-custom-speech-commands-select-custom-voice.md)
