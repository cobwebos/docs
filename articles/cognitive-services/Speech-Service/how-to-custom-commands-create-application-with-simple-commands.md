---
title: 如何：通过简单的命令创建应用程序-语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何使用简单的命令创建和测试托管自定义命令应用程序。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 7f98b2c94659c083f5dbd2c43fd9015f8b60a9db
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307431"
---
# <a name="create-application-with-simple-commands"></a>通过简单的命令创建应用程序

在本文中，学习如何：
 - 创建空应用程序
 - 更新 LUIS 资源
 - 向自定义命令应用程序添加一些基本命令

## <a name="create-empty-application"></a>创建空应用程序
创建空的自定义命令应用程序。 有关详细信息，请参阅[快速入门](quickstart-custom-commands-application.md)。 此时，不会导入项目，而是创建一个空项目。

   > [!div class="mx-imgBorder"]
   > ![创建项目](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>更新 LUIS 资源（可选）

您可以更新在 "**新建项目**" 窗口中选择的创作资源，并设置一个预测资源。 发布自定义命令应用程序时，预测资源用于识别。 在开发和测试阶段，无需预测资源。

## <a name="add-turnon-command"></a>添加 TurnOn 命令

在**智能空间精简**自定义命令应用程序中，添加一个简单的命令，用于处理查询文本、 `turn on the tv` 和响应消息 `Ok, turning the tv on` 。

1. 通过选择左侧窗格顶部的 "**新建命令**" 来创建新命令。 此时将打开**新的命令**窗口。
1. 为 "**名称**" 字段提供值作为**TurnOn**。
1. 选择“创建”。

中间窗格列出了命令的不同属性。 配置命令的以下属性。 有关命令的所有配置属性的说明，请参阅 "[引用](./custom-commands-references.md)"。

| 配置            | 说明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **示例句子** | 示例最谈话用户可以说触发此命令                                                                 |
| **参数**       | 完成命令所需的信息                                                                                |
| **完成规则** | 要执行以执行命令的操作。 例如，要响应用户或与另一个 web 服务进行通信。 |
| **交互规则**   | 用于处理更为具体或复杂的情况的其他规则                                                              |


> [!div class="mx-imgBorder"]
> ![创建命令](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>添加示例句子

让我们从 "**示例句子**" 部分开始，并提供用户可以说出的示例。

1. 在中间窗格中选择 "**示例句子**" 部分。
1. 在最右侧的窗格中，添加示例：

    ```
    turn on the tv
    ```

1.  选择窗格顶部的 "**保存**"。

目前，我们没有参数，因此可以移动到**完成规则**部分。

### <a name="add-a-completion-rule"></a>添加完成规则

接下来，该命令需要具有完成规则。 此规则告知用户正在执行执行操作。 若要阅读有关规则和完成规则的详细信息，请参阅 "[参考](./custom-commands-references.md)"。

1. 选择 "完成" "默认完成规则" 并按如下所示对其**进行**编辑： 

    
    | 设置    | 建议的值                          | 描述                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Name**       | ConfirmationResponse                  | 描述规则用途的名称          |
    | **条件** | 无                                     | 确定何时可以运行规则的条件    |
    | **操作**    | > 简单编辑器 > 第一种变体发送语音响应 >`Ok, turning the tv on` | 规则条件为 true 时要执行的操作 |
    

1. 或者，您可以通过选择中间窗格顶部的 "**添加**"，删除现有的默认完成规则并创建一个新规则。
1. 在 "**名称**" 部分中提供值。
1. 添加操作。
   1. 通过选择 "**操作**" 部分中的 "**添加操作**" 来创建操作。
   1. 在 "**新建操作**" 窗口的 "**类型**" 列表中，选择 "**发送语音响应**"。
   1. 在 "**响应**" 下，选择 "**简单编辑器**"。
   1. 在**第一个变体**字段中，为 response 提供值 `Ok, turning the tv on` 。

   > [!div class="mx-imgBorder"]
   > ![创建语音响应](media/custom-commands/create-speech-response-action.png)

1. 选择 "**保存**" 以保存操作。
1. 返回 "**完成规则**" 部分，选择 "**保存**" 以保存所有更改。 


### <a name="try-it-out"></a>试用

使用 "测试聊天" 面板测试行为
1. 选择位于右窗格顶部的 "**定型**" 图标。
1. 完成训练后，选择 "**测试**"。 通过语音/文本尝试以下查询文本：
    - 输入：打开电视
    - 输出：好，打开电视


> [!div class="mx-imgBorder"]
> ![测试 web 聊天](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> 在 "测试" 面板中，可以选择 "**打开详细**信息" 以获取有关如何处理此语音/文本输入的信息。  

## <a name="add-settemperature-command"></a>添加 SetTemperature 命令

现在，再添加一个命令**SetTemperature** ，它将采用单个查询文本， `set the temperature to 40 degrees` 并使用消息进行响应 `Ok, setting temperature to 40 degrees` 。

按照**TurnOn**命令中所示的步骤，使用示例句子 "**将温度设置为40度**" 来创建新命令。

然后，按如下所示**编辑现有完成**规则：

| 设置    | 建议的值                          |
| ---------- | ---------------------------------------- |
| 名称  | ConfirmationResponse                  |
| 条件 | 无                                     |
| 操作    | > 简单编辑器 > 第一种变体发送语音响应 >`Ok, setting temperature to 40 degrees` |

选择 "**保存**" 以保存对命令所做的所有更改。

## <a name="add-setalarm-command"></a>添加 SetAlarm 命令
使用示例句子创建新的命令**SetAlarm** ，"**为明天的上午9点设置警报**"。 然后，按如下所示**编辑现有完成**规则：

| 设置    | 建议的值                          |
| ---------- | ---------------------------------------- |
| 规则名称  | ConfirmationResponse                  |
| 条件 | 无                                     |
| 操作    | > 简单编辑器 > 第一种变体发送语音响应 >`Ok, setting an alarm for 9 am tomorrow` |

选择 "**保存**" 以保存对命令所做的所有更改。

## <a name="try-it-out"></a>试用

使用 "测试聊天" 面板测试行为
1. 选择“训练”。 训练成功后，请选择 "**测试**"，然后尝试：
    - 键入：将温度设置为40度
    - 预期响应：正常，将温度设置为40度
    - 键入：打开电视
    - 预期响应： Ok，打开电视
    - 键入：将警报设置为明天上午9点
    - 预期响应：好，将警报设置为明天上午9点

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：向命令添加参数](./how-to-custom-commands-add-parameters-to-commands.md)
