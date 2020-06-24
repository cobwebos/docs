---
title: 如何：将语言生成模板用于语音响应-语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何通过自定义命令使用语言生成模板。 语言生成模板允许自定义发送到客户端的响应，并在响应中引入变体。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0cbc57922b31f1b3879bb2cad8a988a1ba4cc368
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307469"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>为语音响应添加语言生成模板

本文介绍如何通过自定义命令使用语言生成模板。 语言生成模板允许自定义发送到客户端的响应，并在响应中引入变体。 语言生成自定义可通过以下方式实现：

- 语言生成模板的使用
- 使用自适应表达式

## <a name="prerequisites"></a>先决条件

你必须已完成以下文章中的步骤：

> [!div class="checklist"]
> * [如何：用简单命令创建应用程序](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [如何：向命令添加参数](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>语言生成模板概述

自定义命令模板基于 BotFramework 的[LG 模板](https://aka.ms/speech/cc-lg-format)。 由于自定义命令会在需要时创建新的 LG 模板（即，对于参数或操作中的语音响应），因此无需指定 LG 模板的名称。 因此，请不要将模板定义为：

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

只需定义不带名称的模板正文，如下所示。

> [!div class="mx-imgBorder"]
> ![模板编辑器示例](./media/custom-commands/template-editor-example.png)


此更改会为发送到客户端的语音响应引入变体。 因此，对于同一查询文本，相应的语音响应会随机选取提供的选项。

利用 LG 模板，还可以使用自适应表达式为命令定义复杂的语音响应。 有关更多详细信息，可以参阅[LG 模板格式](https://aka.ms/speech/cc-lg-format)。 默认情况下，自定义命令支持所有功能，但有以下细微差异：

* 在 LG 模板中，实体表示为 $ {entityName}。 在自定义命令中，我们不使用实体，但参数可用作以下表示形式之一： $ {parameterName} 或 {parameterName}
* 自定义命令不支持模板组合和扩展。 这是因为，你永远不会 `.lg` 直接编辑文件，只是自动创建的模板的响应。
* 自定义命令不支持 LG 插入的自定义函数。 仍支持预定义函数。
* 自定义命令不支持选项（strict、replaceNull & lineBreakStyle）。

## <a name="add-template-responses-to-turnonoff-command"></a>向 TurnOnOff 命令添加模板响应

修改**TurnOnOff**命令以添加具有以下配置的新参数：

| 设置            | 建议的值       | 
| ------------------ | --------------------- | 
| 名称               | `SubjectContext`         | 
| 为全局          | unchecked             | 
| 必需           | unchecked               | 
| 类型               | 字符串                |
| 默认值      | `all` |
| Configuration      | 接受来自内部目录的预定义输入值 | 
| 预定义的输入值 | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>修改完成规则

编辑现有完成规则**ConfirmationResponse**的 "**操作**" 部分。 在 "**编辑操作**" 弹出窗口中，切换到**模板编辑器**并将文本替换为以下示例。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

按如下所示**训练**并**测试**应用程序。 请注意，由于使用了模板值的多个替代项，并使用了自适应表达式，因此响应变体。

* 输入：打开电视
* 输出：好，打开电视
* 输入：打开电视
* 输出：已完成，已打开电视
* 输入：关闭光源
* 输出：确定，关闭所有灯光
* 输入：关闭房间光源
* 输出：确定，关闭房间灯光

## <a name="use-custom-voice"></a>使用自定义语音

自定义自定义命令响应的另一种方法是选择自定义输出声音。 使用以下步骤将默认语音切换为自定义语音。

1. 在自定义命令应用程序的左窗格中，选择 "**设置**"。
1. 从中间窗格中选择 "**自定义语音**"。
1. 从表中选择所需的自定义或公共语音。
1. 选择“保存”。

> [!div class="mx-imgBorder"]
> ![带参数的示例句子](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - 对于**公共语音**，**神经类型**仅适用于特定区域。 若要查看可用性，请参阅[按区域/终结点的标准和神经声音](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。
> - 对于**自定义**语音，可以从自定义语音项目页创建。 请参阅[自定义语音入门](./how-to-custom-voice.md)。

现在，应用程序将在选定的语音中做出响应，而不是默认的语音。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用语音 SDK 集成自定义命令](./how-to-custom-commands-setup-speech-sdk.md)。