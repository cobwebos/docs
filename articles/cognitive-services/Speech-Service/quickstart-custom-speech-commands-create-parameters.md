---
title: 快速入门：使用参数创建自定义命令（预览）-语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，你将向自定义命令应用程序添加参数。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853617"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>快速入门：使用参数创建自定义命令应用程序（预览）

在[前面的文章](./quickstart-custom-speech-commands-create-new.md)中，已创建了一个没有参数的简单自定义命令应用程序。

在本文中，你将扩展此应用程序以使用参数，使其能够处理打开和关闭多个设备的操作。

## <a name="create-parameters"></a>Create Parameters

1. 打开[之前创建](./quickstart-custom-speech-commands-create-new.md)的项目
1. 让我们编辑现有命令以打开和关闭多个设备。
1. 由于该命令现在将处理 on 和 off，因此请将该`TurnOnOff`命令重命名为。
   - 在左窗格中，选择该`TurnOn`命令，然后单击窗格`...`顶部旁边`+ New command`的 "图标"。
   
   - 选择`Rename` "图标"。 在 "**重命名" 命令**弹出窗口中，将`TurOnOff`"**名称**" 更改为。 接下来，选择“保存”****。

1. 接下来，创建一个新参数，用于表示用户是要打开还是关闭设备。
   - 选择`+ Add`中间窗格顶部的图标。 从下拉选择 "**参数**"。
   - 在最右侧的窗格中，可以看到 "**参数**配置" 部分。
   - 为 "**名称**" 添加值。
   - 选中 "**必需**" 复选框。 在 "**为所需参数添加响应**" 窗口中，选择 "**简单编辑器**" 并选择**第一种变体**，添加
        ```
        On or Off?
        ```
   - 选择“更新”  。

       > [!div class="mx-imgBorder"]
       > ![创建所需的参数响应](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - 接下来，让我们按如下所示配置参数的其余属性，并`Save`选择将配置所有配置保存到参数中。
       

       | 配置      | 建议的值     | 说明                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 名称               | 麦克风           | 参数的描述性名称                                                                           |
       | 为全局          | unchecked       | 指示此参数的值是否全局应用于应用程序中所有命令的复选框|
       | 必选           | checked         | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值 |
       | 响应所需的参数      |简单编辑器-> 打开或关闭？      | 在未知情况下询问此参数值的提示 |
       | 类型               | 字符串          | 参数的类型，例如 Number、String、Date Time 或 Geography   |
       | 配置      | 接受来自内部目录的预定义输入值 | 对于字符串，这会将输入限制为一组可能值 |
       | 预定义的输入值     | on, off             | 可能的值及其别名集         |
       
        > [!div class="mx-imgBorder"]
        > ![Create 参数](media/custom-speech-commands/create-on-off-parameter.png)

   - 接下来，再次`+ Add`选择图标以添加另一个参数，以表示具有以下配置的设备的名称。
   

       | 设置            | 建议的值       | 说明                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | 名称               | SubjectDevice         | 参数的描述性名称                                                                     |
       | 为全局          | unchecked             | 指示此参数的值是否全局应用于应用程序中所有命令的复选框 |
       | 必选           | checked               | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值          |
       | 简单编辑器      | 哪个设备？    | 在未知情况下询问此参数值的提示                                       |
       | 类型               | 字符串                | 参数的类型，例如 Number、String、Date Time 或 Geography                                                |
       | 配置      | 接受来自内部目录的预定义输入值 | 对于字符串，字符串列表将输入限制为一组可能值       |
       | 预定义的输入值 | 电视、风扇               | 可能的值及其别名集                               |
       | 别名（电视）      | 电视，telly     | 预定义输入值的每个可能值的可选别名                                 |

## <a name="add-example-sentences"></a>添加示例句子

对于带有参数的命令，添加涵盖所有可能组合的示例句子很有用。 例如：

1. 完整的参数信息-`turn {OnOff} the {SubjectDevice}`
1. 部分参数信息-`turn it {OnOff}`
1. 无参数信息-`turn something`

具有不同信息度的示例句子允许自定义命令应用程序解析包含部分信息的一次性解析和多轮解析。

考虑到这一点，按如下所示编辑示例句子以使用这些参数。

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> 示例句子编辑器使用大括号来引用参数。 - `turn {OnOff} the {SubjectDevice}`使用选项卡自动完成由以前创建的参数支持的功能。

## <a name="add-parameters-to-completion-rules"></a>向完成规则添加参数

修改在[前面的快速入门](./quickstart-custom-speech-commands-create-new.md)中创建的完成规则。

1. 在 "**条件**" 部分中，通过选择 " **+ 添加条件**" 来添加新条件
1. 在新的弹出**新条件**中，从 " `Required parameters` **类型**" 下拉框中选择。 在下面的检查列表中，检查`OnOff`和。 `SubjectDevice`
1. 单击“创建”。****
1. 在 "**操作**" 部分中，通过将鼠标悬停在操作上并单击 "编辑" 图标来编辑现有的 "发送语音响应" 操作。 这一次，我们将使用新创建`OnOff`的和`SubjectDevice`参数

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>试试看
1. 选择`Train`位于右窗格顶部的图标。

1. 训练完成后，请选择`Test`。
    - 将显示一个新的**应用程序**窗口。
    - 尝试几个交互。

        - 输入：关闭电视
        - 输出：确定，关闭电视        
        - 输入：关闭电视
        - 输出：确定，关闭电视
        - 输入：将其关闭
        - 输出：哪些设备？
        - 输入：电视
        - 输出：确定，关闭电视

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：将自定义命令用于自定义语音（预览）](./quickstart-custom-speech-commands-select-custom-voice.md)
