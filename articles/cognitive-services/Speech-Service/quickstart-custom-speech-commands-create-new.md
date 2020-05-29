---
title: 快速入门：创建自定义命令预览版应用语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何创建和测试托管自定义命令预览应用程序。 应用程序将处理最谈话。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 80111ff370f3a5412b45adc04c82c9dee103c01d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142344"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>快速入门：创建自定义命令预览应用

在本快速入门中，你将了解如何创建和测试自定义命令应用程序。
应用程序将处理最谈话，如 "打开电视"，并使用简单消息（如 "正常，打开电视"）进行回复。

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">创建 Azure 语音资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > 目前，自定义命令仅支持 westus、westus2 和 neur 区域中的语音订阅。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>转到 Speech Studio 以获取自定义命令

1. 在 web 浏览器中，转到[Speech Studio](https://speech.microsoft.com/)。
1. 输入登录到门户所需的凭据。

   默认视图是您的语音订阅列表。
    > [!NOTE]
    > 如果看不到 "选择订阅" 页，可以通过在屏幕顶部的 "设置" 菜单中选择 "**语音资源**" 来获取。

1. 选择您的语音订阅，然后选择 "**转到工作室**"。
1. 选择 "**自定义命令**"。

     默认视图是在所选订阅中拥有的自定义命令应用程序的列表。

## <a name="create-a-custom-commands-project"></a>创建自定义命令项目

1. 选择 "**新建项目**" 以创建项目。

   > [!div class="mx-imgBorder"]
   > ![创建项目](media/custom-speech-commands/create-new-project.png)

1. 在 "**名称**" 框中，输入项目名称。
1. 在 "**语言**" 列表中，选择一种语言。
1. 在 " **LUIS 创作资源**" 列表中，选择 "创作资源"。 如果没有有效的创作资源，请选择 "**创建新的 LUIS 创作资源**" 创建一个资源。

   > [!div class="mx-imgBorder"]
   > ![创建资源](media/custom-speech-commands/create-new-resource.png)

   1. 在 "**资源名称**" 框中，输入资源的名称。
   1. 在 "**资源组**" 列表中，选择一个资源组。
   1. 在 "**位置**" 列表中，选择一个位置。
   1. 在 "**定价层**" 列表中，选择一个层。

      > [!NOTE]
      > 可以通过在 "**资源组**" 框中输入资源组名称来创建资源组。 选择 "**创建**" 后，将创建资源组。

1. 选择“创建” 。
1. 创建项目后，将其选中。

    你现在应该会看到新的自定义命令应用程序的概述。

## <a name="update-luis-resources-optional"></a>更新 LUIS 资源（可选）

您可以更新在 "**新建项目**" 窗口中选择的创作资源并设置预测资源。 发布自定义命令应用程序时，预测资源用于识别。 在开发和测试阶段，无需预测资源。

1. 在左窗格中选择 "**设置**"，然后在中间窗格中选择 " **LUIS 资源**"。
1. 选择一个预测资源，或选择 "**创建新资源**" 创建一个。
1. 选择“保存” 。
    
    > [!div class="mx-imgBorder"]
    > ![设置 LUIS 资源](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> 由于创作资源每月仅支持1000预测终结点请求，因此在发布自定义命令应用程序之前，需要设置 LUIS 预测资源。


## <a name="create-a-command"></a>创建命令

让我们创建一个简单的命令，该命令将使用单个查询文本， `turn on the tv` 并使用消息进行 `Ok, turning on the tv` 响应。

1. 通过选择左侧窗格顶部的 "**新建命令**"，创建一个命令。 此时将打开**新的命令**窗口。
1. 在 "**名称**" 框中，输入**TurnOn**。
1. 选择“创建” 。

中间窗格列出命令的属性：


| 配置            | 说明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **示例句子** | 最谈话的示例，用户可以说触发命令。                                                                 |
| **参数**       | 完成命令所需的信息。                                                                                |
| **完成规则** | 要执行以执行命令的操作。 例如，响应用户或与其他 web 服务通信。 |
| **交互规则**   | 用于处理更为具体或复杂的情况的其他规则。                                                              |


> [!div class="mx-imgBorder"]
> ![创建命令](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>添加示例句子

让我们从**示例句子**部分开始。 我们将提供用户可以说出的示例。

1. 在中间窗格中选择 "**示例句子**"。 
1. 在右侧窗格中，添加示例：

    ```
    turn on the tv
    ```

1. 选择窗格顶部的 "**保存**"。

目前，我们没有参数，因此可以移动到**完成规则**部分。

### <a name="add-a-completion-rule"></a>添加完成规则

现在，添加具有以下配置的完成规则。 此规则告知用户正在执行执行操作。


| 设置    | 建议的值                          | 描述                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **名称**  | **ConfirmationResponse**                  | 描述规则用途的名称。          |
| **条件** | 无                                     | 确定何时可以运行规则的条件。    |
| **操作**    | **发送语音响应-> 确定，打开电视** | 规则条件为 true 时要执行的操作。 |

1. 通过选择中间窗格顶部的 "**添加**" 来创建新的完成规则。
1. 在“名称”**** 框中，键入名称。
1. 添加操作。
   1. 通过选择 "**操作**" 部分中的 "**添加操作**" 来创建操作。
   1. 在 "**编辑操作**" 窗口的 "**类型**" 列表中，选择 "**发送语音响应**"。
   1. 在 "**响应**" 下，选择 "**简单编辑器**"。 在**第一个变体**框中，输入 **"确定"，打开电视**。

   > [!div class="mx-imgBorder"]
   > ![创建响应](media/custom-speech-commands/create-speech-response-action.png)

1. 选择 "**保存**" 以保存规则。
1. 返回 "**完成规则**" 部分，选择 "**保存**" 以保存所有更改。 

> [!div class="mx-imgBorder"]
> ![创建完成规则](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>试试看

使用 "测试聊天" 面板测试行为。
1. 选择右窗格顶部的 "**定型**"。
1. 完成训练后，选择 "**测试**"。 此时将显示一个新的 "**测试应用程序**" 窗口。
    - 进入**电视**
    - 预期响应：**确定，打开电视**


> [!div class="mx-imgBorder"]
> ![测试行为](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：使用参数创建自定义命令预览应用程序](./quickstart-custom-speech-commands-create-parameters.md)
