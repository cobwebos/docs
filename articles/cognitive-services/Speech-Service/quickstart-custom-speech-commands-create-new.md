---
title: 快速入门：创建自定义命令（预览）-语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何创建和测试托管自定义命令应用程序。
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: d8e28b88757fa7557b04ee471ede17012094bb9e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446876"
---
# <a name="quickstart-create-a-custom-command-preview"></a>快速入门：创建自定义命令（预览）

本文介绍如何创建和测试托管自定义命令应用程序。
该应用程序将识别查询文本，例如 "打开电视"，并使用简单消息 "正常，打开电视" 来做出响应。

## <a name="prerequisites"></a>必备组件

- 语音订阅。 

如果没有语音订阅，可以通过导航到[Speech Studio](https://speech.microsoft.com/)并选择 "**创建语音资源**" 来创建一个。

  > [!div class="mx-imgBorder"]
  > [![创建项目](media/custom-speech-commands/create-new-subscription.png)](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > 预览期间，仅支持 westus2 区域。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>转到 Speech Studio 以获取自定义命令

1. 打开 web 浏览器并导航到[Speech Studio](https://speech.microsoft.com/)
1. 输入你的凭据以登录到门户

   - 默认视图是您的语音订阅列表
     > [!NOTE]
     > 如果看不到 "选择订阅" 页，可以通过在顶部栏上的 "设置" 菜单中选择 "语音资源" 来进行导航。

1. 选择你的语音订阅，然后选择 "**转到工作室**"
1. 选择**自定义命令（预览）**

默认视图是你创建的自定义命令应用程序的列表。

## <a name="create-a-custom-commands-project"></a>创建自定义命令项目

1. 选择 "**新建项目**" 以创建新项目

   > [!div class="mx-imgBorder"]
   > ![创建项目](media/custom-speech-commands/create-new-project.png)

1. 输入项目名称和语言。
1. 选择创作资源。 如果没有有效的创作资源，请选择 "**创建新资源**" 创建一个。

   > [!div class="mx-imgBorder"]
   > ![创建资源](media/custom-speech-commands/create-new-resource.png)

   1. 输入资源名称、组、位置和定价层。

         > [!NOTE]
         > 可以通过在 "资源组" 字段中输入所需的资源组名称来创建资源组。 选择 "**创建**" 后，将创建资源组。

1. 单击 "**创建**" 以创建项目。
1. 创建后，选择你的项目。

现在，你的视图应为自定义命令应用程序的概述。

## <a name="update-luis-resources-optional"></a>更新 LUIS 资源（可选）

您可以在 "新建项目" 窗口中更新创作资源集，并设置用于在运行时识别输入的预测资源。 

> [!NOTE]
> 你需要先设置预测资源，然后应用程序请求的预测超出了创作资源提供的1000请求。

> [!div class="mx-imgBorder"]
> ![设置 LUIS 资源](media/custom-speech-commands/set-luis-resources.png)

1. 通过从左窗格中选择 "**设置**"，然后从中间窗格**LUIS 资源**，导航到 "LUIS 资源" 窗格。
1. 选择一个预测资源，或选择 "**创建新资源**" 创建一个
1. 选择“保存”

## <a name="create-a-new-command"></a>创建新命令

现在，你可以创建一个命令。 让我们使用一个示例，该示例将使用单个查询文本、`turn on the tv`，并使用 `Ok, turning on the TV`的消息进行响应。

1. 通过选择 "命令" 旁边的 "`+`" 图标并为其指定名称来创建新命令 `TurnOn`
1. 选择“保存”

> [!div class="mx-imgBorder"]
> ![创建命令](media/custom-speech-commands/create-add-command.png)

命令是一组：

| 组            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 例句 | 示例最谈话用户可以说触发此命令                                                                 |
| 参数       | 完成命令所需的信息                                                                                |
| 完成规则 | 要执行以执行命令的操作。 例如，要响应用户或与其他 web 服务通信 |
| 高级规则   | 用于处理更为具体或复杂的情况的其他规则                                                              |

### <a name="add-a-sample-sentence"></a>添加示例句子

让我们从示例句子开始，提供用户可以说出的示例：

```
turn on the tv
```

目前，我们没有参数，因此可以继续使用完成规则。

### <a name="add-a-completion-rule"></a>添加完成规则

现在，添加一个完成规则以响应用户指示正在执行某一操作。

1. 通过选择 "完成规则" 旁边的 "`+`" 图标来创建新的完成规则
1. 输入规则名称
1. 添加操作
   1. 通过选择 "操作" 旁边的 `+` 图标来创建新的语音响应操作，然后选择 "`SpeechResponse`
   1. 输入响应

   > [!NOTE]
   > 常规文本必须以破折号开头。 有关更多详细信息，请参阅[此处](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![创建语音响应](media/custom-speech-commands/create-speech-response-action.png)

1. 单击 "**保存**" 以保存规则

> [!div class="mx-imgBorder"]
> ![创建完成规则](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 设置    | 建议的值                          | Description                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 规则名称  | "ConfirmationResponse"                   | 描述规则用途的名称          |
| 条件 | 无                                     | 确定何时可以运行规则的条件    |
| 操作    | SpeechResponse "-正常，启用电视" | 规则条件为 true 时要执行的操作 |

## <a name="try-it-out"></a>试试看

使用 "测试聊天" 面板测试行为。

> [!div class="mx-imgBorder"]
> 通过 web 聊天](media/custom-speech-commands/create-basic-test-chat.png) ![测试

- 键入 "打开电视"
- 预期响应： "正常，打开电视"

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
