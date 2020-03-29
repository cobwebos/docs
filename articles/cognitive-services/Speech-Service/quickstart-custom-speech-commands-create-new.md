---
title: 快速入门：创建自定义命令（预览） - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，您将创建和测试托管的自定义命令应用程序。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155581"
---
# <a name="quickstart-create-a-custom-command-preview"></a>快速入门：创建自定义命令（预览）

在本文中，您将学习如何创建和测试托管的自定义命令应用程序。
该应用程序将识别诸如"打开电视"之类的话语，并回复一条简单的消息"好的，打开电视"。

## <a name="prerequisites"></a>先决条件

- 语音订阅。

如果没有语音订阅，可以通过导航到[语音工作室](https://speech.microsoft.com/)并选择 **"创建语音资源**"来创建一个订阅。

  > [!div class="mx-imgBorder"]
  > [![创建项目](media/custom-speech-commands/create-new-subscription.png)](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > 在预览期间，仅支持 Westus2 区域。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>转到语音工作室获得自定义命令

1. 打开 Web 浏览器，然后导航到[语音工作室](https://speech.microsoft.com/)
1. 输入要登录到门户的凭据

   - 默认视图是语音订阅列表
     > [!NOTE]
     > 如果看不到"选择订阅"页，则可以通过从顶部栏的"设置"菜单中选择"语音资源"来导航。

1. 选择语音订阅，然后选择 **"转到工作室**"
1. 选择**自定义命令（预览）**

默认视图是您创建的自定义命令应用程序的列表。

## <a name="create-a-custom-commands-project"></a>创建自定义命令项目

1. 选择 **"新建项目**"以创建新项目

   > [!div class="mx-imgBorder"]
   > ![创建项目](media/custom-speech-commands/create-new-project.png)

1. 输入项目名称和语言。
1. 选择创作资源。 如果没有有效的创作资源，请通过选择 **"创建新资源"** 来创建一个资源。

   > [!div class="mx-imgBorder"]
   > ![创建资源](media/custom-speech-commands/create-new-resource.png)

   1. 输入资源名称、组、位置和定价层。

         > [!NOTE]
         > 您可以通过在"资源组"字段中输入所需的资源组名称来创建资源组。 选择 **"创建"** 后，将创建资源组。

1. 单击"**创建**"以创建项目。
1. 创建后，选择项目。

现在，您的视图应该是自定义命令应用程序的概述。

## <a name="update-luis-resources-optional"></a>更新 LUIS 资源（可选）

您可以在新项目窗口中更新创作资源集，并设置用于在运行时识别输入的预测资源。

> [!NOTE]
> 您需要在应用程序请求超出创作资源提供的 1，000 个请求之前设置预测资源。

> [!div class="mx-imgBorder"]
> ![设置 LUIS 资源](media/custom-speech-commands/set-luis-resources.png)

1. 通过从左侧窗格中选择 **"设置"，** 然后从中间窗格中选择 LUIS 资源，导航到**LUIS 资源**窗格。
1. 选择预测资源，或通过选择 **"创建新资源"** 创建一个资源
1. 选择 **"保存"**

## <a name="create-a-new-command"></a>创建新命令

现在，您可以创建命令。 让我们使用一个示例，该示例将采用单个陈述，`turn on the tv`并使用消息`Ok, turning on the TV`进行响应。

1. 通过选择命令旁边的`+`图标并为其指定名称来创建新命令`TurnOn`
1. 选择 **"保存"**

> [!div class="mx-imgBorder"]
> ![创建命令](media/custom-speech-commands/create-add-command.png)

命令是一组：

| 组            | 描述                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 示例句子 | 用户可以说触发此命令的示例陈述                                                                 |
| 参数       | 完成命令所需的信息                                                                                |
| 完成规则 | 要执行命令的操作。 例如，响应用户或与其他 Web 服务通信 |
| 高级规则   | 处理更具体或复杂情况的其他规则                                                              |

### <a name="add-a-sample-sentence"></a>添加示例句子

让我们从示例句子开始，并提供用户可以说的示例：

```
turn on the tv
```

现在，我们没有参数，因此我们可以转到完成规则。

### <a name="add-a-completion-rule"></a>添加完成规则

现在添加一个完成规则以响应指示正在执行的操作的用户。

1. 通过选择"完成规则"旁边的图标`+`创建新的完成规则
1. 输入规则名称
1. 添加操作
   1. 通过选择"操作"旁边的`+`图标并选择"操作"，创建新的语音响应操作`SpeechResponse`
   1. 输入响应

   > [!NOTE]
   > 常规文本必须以破折号开头。 有关详细信息，请[转到此处](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![创建语音响应](media/custom-speech-commands/create-speech-response-action.png)

1. 单击 **"保存"** 以保存规则

> [!div class="mx-imgBorder"]
> ![创建完成规则](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 设置    | 建议的值                          | 描述                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 规则名称  | "确认回复"                   | 描述规则目的的名称          |
| 条件 | 无                                     | 确定规则何时可以运行的条件    |
| 操作    | 语音响应 "-好的，打开电视" | 规则条件为 true 时要执行的操作 |

## <a name="try-it-out"></a>试用

使用"测试聊天"面板测试行为。

> [!div class="mx-imgBorder"]
> ![使用网络聊天进行测试](media/custom-speech-commands/create-basic-test-chat.png)

- 键入："打开电视"
- 预期回应："好的，打开电视"

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
