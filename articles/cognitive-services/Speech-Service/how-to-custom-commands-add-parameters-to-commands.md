---
title: 如何：向自定义命令添加简单的命令应用程序-语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将参数添加到自定义命令中
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0ed237debc2395ed307658b2d57a541574f9478a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284143"
---
# <a name="add-parameters-to-commands"></a>向命令添加参数

本文介绍如何将参数添加到自定义命令。 参数是命令完成任务所需的信息。 在复杂的方案中，还可以使用参数定义触发自定义操作的条件。

## <a name="prerequisites"></a>必备条件

> [!div class="checklist"]
> * [如何：用简单命令创建应用程序](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>为 TurnOn 命令配置参数

编辑现有的 **TurnOn** 命令以打开和关闭多个设备。

1. 因为该命令现在将同时处理 on 和 off 方案，请将命令重命名为 **TurnOnOff**。
   1. 在左窗格中，选择 " **TurnOn** " 命令，然后选择窗格顶部的 " **新建命令** " 旁的省略号 ( ... ") 。
   
   1. 选择 " **重命名**"。 在 " **重命名" 命令** 窗口中，将 " **名称** " 更改为 **TurnOnOff**。

1. 接下来，向此命令添加一个新参数，该参数表示用户是要打开还是关闭设备。
   1. 选择中间窗格顶部的 "  **添加** "。 从下拉选择 " **参数**"。
   1. 在右窗格的 " **参数** " 部分中，在 " **名称** " 框中添加 " **麦克风**" 值。
   1. 选择 " **必需**"。 在 " **为所需参数添加响应** " 窗口中，选择 " **简单编辑器**"。 在 **第一**种情况下，添加
        ```
        On or Off?
        ```
   1. 选择“更新”  。

       > [!div class="mx-imgBorder"]
       > ![创建所需的参数响应](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 现在，我们配置参数属性。 有关命令的所有配置属性的说明，请参阅 " [引用](./custom-commands-references.md)"。 配置参数的属性，如下所示：
      

       | Configuration      | 建议的值     | 说明                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 名称               | `OnOff`           | 参数的描述性名称                                                                           |
       | 为全局          | unchecked       | 指示此参数的值是否全局应用于应用程序中所有命令的复选框|
       | 必须           | checked         | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值 |
       | 响应所需的参数      |简单编辑器 > `On or Off?`      | 在未知情况下询问此参数值的提示 |
       | 类型               | 字符串          | 参数的类型，例如 Number、String、Date Time 或 Geography   |
       | Configuration      | 接受来自内部目录的预定义输入值 | 对于字符串，这会将输入限制为一组可能值 |
       | 预定义的输入值     | `on`, `off`           | 可能的值及其别名集         |
       
        
   1. 要添加预定义的输入值，请选择 " **添加预定义的输入** "，并在 " **新建项**  " 窗口中键入上表中提供的 **名称** 。 在这种情况下，我们不使用别名，因此可将其留空。
   
      > [!div class="mx-imgBorder"]
      > ![Create 参数](media/custom-commands/create-on-off-parameter.png)

   1. 选择 " **保存** " 以保存参数的所有配置。
 
 ### <a name="add-subjectdevice-parameter"></a>添加 SubjectDevice 参数 

   1. 接下来，再次选择 " **添加** " 以添加一个参数，用于表示可使用此命令控制的设备的名称。 使用以下配置。
   

       | 设置            | 建议的值       |
       | ------------------ | --------------------- |
       | 名称               | `SubjectDevice`         |
       | 为全局          | unchecked             |
       | 必须           | checked               |
       | 响应所需的参数     | 简单编辑器 > `Which device do you want to control?`    | 
       | 类型               | 字符串                |          |
       | Configuration      | 接受来自内部目录的预定义输入值 | 
       | 预定义的输入值 | `tv`, `fan`               |
       | 别名 (`tv`)       | `television`, `telly`     |

   1. 选择“保存”

### <a name="modify-example-sentences"></a>修改示例句子

对于带有参数的命令，添加涵盖所有可能组合的示例句子很有用。 例如：

* 完整的参数信息- `turn {OnOff} the {SubjectDevice}`
* 部分参数信息- `turn it {OnOff}`
* 无参数信息- `turn something`

具有不同信息度的示例句子允许自定义命令应用程序解析包含部分信息的一次性解析和多轮解析。

考虑到这一点，按如下所示编辑示例句子以使用参数：

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

选择“保存”。

> [!TIP]
> 示例句子编辑器使用大括号来引用参数。 - `turn {OnOff} the {SubjectDevice}` 使用选项卡自动完成由以前创建的参数支持的功能。

### <a name="modify-completion-rules-to-include-parameters"></a>修改完成规则以包含参数

修改现有完成规则 **ConfirmationResponse**。

1. 在 " **条件** " 部分中，选择 " **添加条件**"。
1. 在 " **新建条件** " 窗口的 " **类型** " 列表中，选择 " **必需参数**"。 在下面的检查列表中，选中 " **麦克风** " 和 " **SubjectDevice**"。
1. 将 **IsGlobal** 保留为未选中状态。
1. 选择“创建”  。
1. 在 " **操作** " 部分中，通过将鼠标悬停在操作上并选择 "编辑" 按钮来编辑现有的 " **发送语音响应** " 操作。 这一次，将使用新创建的 **麦克风** 和 **SubjectDevice** 参数

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. 选择“保存”。

### <a name="try-it-out"></a>试试看
1. 选择右窗格顶部的 " **训练** " 图标。

1. 训练完成后，选择 " **测试**"。 **您的应用程序**窗口将出现。
 尝试几个交互。

    - 输入：关闭电视
    - 输出：确定，关闭电视
    - 输入：关闭电视
    - 输出：确定，关闭电视
    - 输入：将其关闭
    - 输出：要控制哪个设备？
    - 输入：电视
    - 输出：确定，关闭电视

## <a name="configure-parameters-for-settemperature-command"></a>为 SetTemperature 命令配置参数

修改 **SetTemperature** 命令，使其能够按用户的指示设置温度。

用以下配置添加新的参数**温度**

| Configuration      | 建议的值     |
| ------------------ | ----------------|
| 名称               | `Temperature`           |
| 必须           | checked         |
| 响应所需的参数      | 简单编辑器 > `What temperature would you like?`
| 类型               | Number          |


将示例最谈话编辑为以下值。

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

按照以下配置编辑现有的完成规则。

| Configuration      | 建议的值     |
| ------------------ | ----------------|
| 条件         | 要求的参数 > 温度           |
| 操作           | 发送语音响应 > `Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>试试看

使用几个交互来**定型**和**测试**更改。

- 输入：设置温度
- 输出：所需的温度是多少？
- 输入：50度
- 输出：正常，将温度设置为50度

## <a name="configure-parameters-to-the-setalarm-command"></a>配置 SetAlarm 命令的参数

使用以下配置添加一个名为 **DateTime** 的参数。

   | 设置                           | 建议的值                     | 
   | --------------------------------- | ----------------------------------------|
   | 名称                              | `DateTime`                               |
   | 必须                          | checked                                 |
   | 响应所需的参数   | 简单编辑器 > `For what time?`            | 
   | 类型                              | DateTime                                |
   | 日期默认值                     | 如果当前缺少日期，请使用            |
   | 时间默认值                     | 如果缺少时间，则使用 "日开始"     |


> [!NOTE]
> 本文主要介绍了如何使用字符串、数字和日期时间参数类型。 对于所有受支持的参数类型及其属性的列表，请参阅 " [引用](./custom-commands-references.md)"。


将最谈话示例编辑为以下值。

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

按照以下配置编辑现有的完成规则。

   | 设置    | 建议的值                               |
   | ---------- | ------------------------------------------------------- |
   | 操作    | 发送语音响应- `Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>试试看

**训练** 和 **测试** 更改。
- 输入：为明天中午设置警报
- 输出：正常，为 2020-05-02 12:00:00 设置的警报
- 输入：设置警报
- 输出：什么时间？
- 输入：下午
- 输出：正常，为 2020-05-01 17:00:00 设置的警报


## <a name="try-out-all-the-commands"></a>尝试所有命令

使用与不同命令相关的最谈话，同时测试所有三个命令。 请注意，你可以在不同的命令之间切换。

- 输入：设置警报
- 输出：什么时间？
- 输入：打开电视
- 输出：好，打开电视
- 输入：设置警报
- 输出：什么时间？
- 输入：下午
- 输出：正常，为 2020-05-01 17:00:00 设置的警报

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：向命令参数添加配置](./how-to-custom-commands-add-parameter-configuration.md)
