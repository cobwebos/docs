---
title: 快速入门：创建自定义命令（预览）-语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何创建和测试托管自定义命令应用程序。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872521"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>快速入门：创建自定义命令应用（预览）

在本快速入门中，你将了解如何创建和测试自定义命令应用程序。
创建的应用程序将处理最谈话，例如 "打开电视"，并使用简单消息 "正常，打开电视" 进行回复。

## <a name="prerequisites"></a>必备条件

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">创建 Azure 语音资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > 目前，自定义命令仅支持 westus、westus2 和 neur 区域中的语音订阅。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>转到 Speech Studio 以获取自定义命令

1. 打开 web 浏览器并导航到[Speech Studio](https://speech.microsoft.com/)
1. 输入登录到门户所需的凭据。

   - 默认视图是您的语音订阅列表。
     > [!NOTE]
     > 如果看不到 "选择订阅" 页，可以通过在顶部栏上的 "设置" 菜单中选择 "语音资源" 来进行导航。

1. 选择您的语音订阅，然后选择 "**转到工作室**"。
1. 选择 "**自定义命令**"。

     - 默认视图是在所选订阅下具有的自定义命令应用程序的列表。

## <a name="create-a-custom-commands-project"></a>创建自定义命令项目

1. 选择 "**新建项目**" 以创建新项目。

   > [!div class="mx-imgBorder"]
   > ![创建项目](media/custom-speech-commands/create-new-project.png)

1. 输入项目名称。
1. 从下拉选择 "语言"。
1. 从下拉选择 "创作资源"。 如果没有有效的创作资源，请单击 "**创建新的 LUIS 创作资源**" 创建一个。

   > [!div class="mx-imgBorder"]
   > ![创建资源](media/custom-speech-commands/create-new-resource.png)

   - 输入资源名称 "资源组"。
   - 从下拉的位置选择 "位置" 和 "定价层"。

      > [!NOTE]
      > 可以通过在 "资源组" 字段中输入所需的资源组名称来创建资源组。 选择 "**创建**" 后，将创建资源组。

1. 接下来，选择 "**创建**" 以创建项目。
1. 创建后，选择你的项目。

    - 现在，你的视图应为新创建的自定义命令应用程序的概述。

## <a name="update-luis-resources-optional"></a>更新 LUIS 资源（可选）

您可以更新在 "新建项目" 窗口中设置的创作资源，并设置一个预测资源。 发布自定义命令应用程序后，预测资源将用于识别。 开发和测试阶段不需要预测资源。

1. 从左窗格中选择 "**设置**"，然后导航到中间窗格中的 " **LUIS 资源**" 部分。
1. 选择一个预测资源，或选择 "**创建新资源**" 创建一个。
1. 选择“保存”。 
    
    > [!div class="mx-imgBorder"]
    > ![设置 LUIS 资源](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> 由于创作资源每月仅支持1000预测终结点请求，因此在发布自定义命令应用程序之前，mandatorily 需要设置 LUIS 预测资源。


## <a name="create-a-new-command"></a>创建新命令

让我们创建一个简单的命令，该命令将使用单个`turn on the tv`查询文本，并使用消息`Ok, turning on the tv`进行响应。

1. 通过选择最左侧窗格中显示`+ New command`的图标来创建新命令。 此时会出现一个新的弹出窗口，其中标题为 "**新命令**"。
1. 为`TurnOn`"**名称**" 字段提供值。
1. 选择“创建”。 

中间窗格登记命令的不同属性：


| 配置            | 说明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 示例句子 | 示例最谈话用户可以说触发此命令                                                                 |
| 参数       | 完成命令所需的信息                                                                                |
| 完成规则 | 要执行以执行命令的操作。 例如，要响应用户或与另一个 web 服务进行通信。 |
| 交互规则   | 用于处理更为具体或复杂的情况的其他规则                                                              |


> [!div class="mx-imgBorder"]
> ![创建命令](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>添加示例句子

让我们从 "示例句子" 部分开始，并提供用户可以说出的示例。

1. 从中间窗格中选择 "**示例句子**" 部分，然后在最右侧的窗格中添加示例：

    ```
    turn on the tv
    ```

1. 选择`Save`此窗格顶部的图标。

目前，我们没有参数，因此，我们可以转到**完成规则**部分。

### <a name="add-a-completion-rule"></a>添加完成规则

现在，使用以下配置添加完成规则。 此规则表明用户正在执行执行操作。


| 设置    | 建议的值                          | 描述                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 规则名称  | ConfirmationResponse                  | 描述规则用途的名称          |
| 条件 | 无                                     | 确定何时可以运行规则的条件    |
| 操作    | SpeechResponse "-正常，启用电视" | 规则条件为 true 时要执行的操作 |

1. 通过选择中间窗格顶部的`+Add`图标来创建新的完成规则。
1. 在 "**名称**" 部分中提供值。
1. 添加操作
   1. 通过选择 "**操作**" 部分中的 " **+ 添加" 操作**创建新操作。
   1. 在 "**新建操作**" 弹出窗口中， `Send speech response`从 "**类型**" 下拉选项中进行选择。
   1. 为`Simple editor` "**响应**" 字段选择。
       - 在**第一个变体**字段中，为 response 提供值`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![创建语音响应](media/custom-speech-commands/create-speech-response-action.png)

1. 单击 "**保存**" 保存规则。
1. 返回 "**完成规则**" 部分，选择 "**保存**" 以保存所有更改。 

> [!div class="mx-imgBorder"]
> ![创建完成规则](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>试试看

使用 "测试聊天" 面板测试行为
1. 选择`Train`位于右窗格顶部的图标。
1. 训练完成后，请选择`Test`。 将显示一个新的**应用程序**窗口。
    - 键入：打开电视
    - 预期响应：确定，打开电视


> [!div class="mx-imgBorder"]
> ![测试 web 聊天](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
