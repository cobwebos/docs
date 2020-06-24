---
title: 如何：向自定义命令添加确认
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在自定义命令中实现命令的确认。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307471"
---
# <a name="add-interaction-rules"></a>添加交互规则

本文介绍**交互规则**。 这些是用于处理更具体或复杂情况的其他规则。 虽然你可以自由创作你自己的自定义交互规则，但在本文中，你将对以下目标方案使用交互规则：

* 确认命令
* 向命令添加一个步骤更正

请参阅[参考](./custom-commands-references.md)部分，了解有关交互规则的详细信息。

## <a name="prerequisites"></a>先决条件

你必须已完成以下文章中的步骤：
> [!div class="checklist"]
> * [如何：用简单命令创建应用程序](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [如何：向命令添加参数](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>向命令添加确认

若要添加确认，请使用**SetTemperature**命令。 若要获得确认，请使用以下步骤创建交互规则。

1. 从左窗格中选择 " **SetTemperature** " 命令。
2. 选择中间窗格中的 "**添加**"，然后选择 "**交互规则**" "确认" 命令，以添加交互规则  >  **Confirm command**。

    这将添加3个交互规则。此规则将要求用户确认警报的日期和时间，并在下一次打开时要求确认（是/否）。

    1. 按照以下配置修改**Confirm 命令**交互规则
        1. 将**名称**重命名为 **`Confirm Temperature`** 。
        1. 将新条件添加为-**所需参数 > 温度**
        1. 添加新操作**类型 > 发送语音响应 > `Are you sure you want to set the temperature as {Temperature} degrees?` **
        1. 保留 "预期" 部分中的 "期望**用户确认**" 的默认值。
      
         > [!div class="mx-imgBorder"]
         > ![创建所需的参数响应](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. 修改**确认成功**交互规则以处理成功的确认（用户说 "是"）。
      
          1. 将**名称**修改为 **`Confirmation temperature succeeded`** 。
          1. 保持已有的**确认已成功**。
          1. **> 必需参数 > 温度**添加新条件
          1. 将**执行后状态**的默认值保留为 "**执行完成规则**"。

    1. 修改**确认被拒绝**（用户未说 "否"），以在确认拒绝时处理方案。

          1. 将**名称**修改为 **`Confirmation temperature denied`** 。
          1. 保留 "已有**确认已拒绝**" 条件。
          1. **> 必需参数 > 温度**添加新条件
          1. 添加新操作**类型 > 发送语音响应 > `No problem. What temperature then?` **
          1. 将**执行后状态**的默认值保留为 "**等待用户输入**"。

> [!IMPORTANT]
> 本文介绍如何使用内置确认功能。 此外，也可以通过手动添加交互规则来实现相同的目的。
   

### <a name="try-out-the-changes"></a>尝试更改

选择 "**定型**"，等待训练完成，然后选择 "**测试**"。

- 输入：将温度设置为80度
- 输出：正常80？
- 输入：否
- 输出：没有问题。 什么是温度？
- 输入：83度
- 输出：正常83？
- 输入：是
- 输出：正常，将温度设置为83度


## <a name="implementing-corrections-in-a-command"></a>在命令中实现更正

在本部分中，将配置一个单步更正，一旦执行操作已执行，就会使用该更正。 还会看到一个示例，说明如何默认启用更正，以防该命令尚未完成。 若要在命令未完成时添加更正，请添加新参数**AlarmTone**。

从左窗格中选择 " **SetAlarm** " 命令并添加一个新参数**AlarmTone**。
        
- **路径名** > `AlarmTone`
- **类型**> 字符串
- **默认值** > `Chimes`
- **配置**> 接受来自内部目录的预定义输入值
- **预定义的输入值**  >  `Chimes` 、 `Jingle` 和 `Echo` 。 每个作为单独的预定义输入。


接下来，将 DateTime 参数的响应更新为 `Ready to set alarm with tone as {AlarmTone}. For what time?` 。 然后按如下所示修改完成规则。

1. 选择现有完成规则 " **ConfirmationResponse**"。
1. 在右侧面板中，将鼠标悬停在现有操作上，然后选择 "**编辑**" 按钮。
1. 更新语音响应`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>尝试更改

选择 "**定型**"，等待训练完成，然后选择 "**测试**"。
尝试以下最谈话：

- 输入：设置警报
- 输出：准备好将音频设置为 Chimes。 什么时间？
- 输入：将音频设置为曲调，为明天9点
- 输出：正常，为 2020-05-30 09:00:00 设置的警报。 警报音为曲调。

> [!IMPORTANT]
> 请注意，在执行命令时无需任何显式配置即可更改警报音，即命令尚未完成。 **默认情况下，对于所有命令参数均启用更正，无论是否要实现该命令的 turn 号，都是如此。**

### <a name="correction-when-command-is-completed"></a>命令完成时进行更正

自定义命令平台还提供了一种单步更正功能，即使在命令完成后也是如此。 但此功能在默认情况下未启用，必须进行显式配置。 使用以下步骤配置单步更正。

1. 在**SetAlarm**命令中，添加类型为**update previous 命令**的交互规则以更新以前设置的警报。 重命名交互规则的默认**名称**，以**更新以前的警报**。
1. 保留默认条件 "**上一命令需要**按原样更新"。
1.  添加新条件作为**类型 > 必需参数 > 日期时间**。
1. 将新操作添加为**类型 > 将语音响应发送 > 简单编辑器 `Updating previous alarm time to {DateTime}.` >**
1. 将 "post 执行状态" 的默认值保留为 "**命令已完成**"。

### <a name="try-out-the-changes"></a>尝试更改

选择 "**定型**"，等待训练完成，然后选择 "**测试**"。

- 输入：设置警报
- 输出：准备好将音频设置为 Chimes。 什么时候？
- 输入：将音频设置为曲调，为明天9点
- 输出：正常，为 2020-05-21 09:00:00 设置的警报。 警报音为曲调。
- 输入：否，上午8点
- 输出：将以前的警报时间更新为 2020-05-29 08:00。

> [!NOTE]
> 在实际应用程序中，在此更正规则的 "操作" 部分中，还需要将活动发送回客户端或调用 HTTP 终结点，以更新系统中的警报时间。 此操作应特别负责仅更新警报时间，而不是任何其他命令属性（在本例中为警报音）。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：为语音响应添加语言生成模板](./how-to-custom-commands-add-language-generation-templates.md)