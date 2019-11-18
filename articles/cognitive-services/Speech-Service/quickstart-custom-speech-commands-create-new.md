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
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 80eb0a2018ece23de80e8eb9c4a68c149b590440
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111369"
---
# <a name="quickstart-create-a-custom-command-preview"></a>快速入门：创建自定义命令（预览）

本文介绍如何创建和测试托管自定义命令应用程序。
该应用程序将识别查询文本，例如 "打开电视"，并使用简单消息 "正常，打开电视" 来做出响应。

## <a name="prerequisites"></a>先决条件

- 语音订阅。 [免费试用语音服务](~/articles/cognitive-services/speech-service/get-started.md)。

  > [!NOTE]
  > 在预览期间，订阅密钥仅支持 westus2 区域。

- [语言理解](https://www.luis.ai/home)（LUIS）创作密钥：
  1. 打开 web 浏览器并导航到[Azure 门户](https://portal.azure.com)
  1. 选择“创建资源”
  1. 搜索并选择[语言理解](https://aka.ms/sc-luis-all)
  1. 选择创建选项中的创作
  1. 部署资源后，请从 "快速启动" 或 "密钥" 部分中转到资源并复制该密钥。

      > [!div class="mx-imgBorder"]
      > ![创建创作资源](media/custom-speech-commands/resources-lu-authoring.png)

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
   > ![创建新项目](media/custom-speech-commands/create-new-project.png)

1. 输入项目名称和语言，然后选择 "**下一步**" 继续
1. 输入 LUIS 创作密钥
1. 创建后，选择你的项目

现在，你的视图应为自定义命令应用程序的概述。

## <a name="create-a-new-command"></a>创建新命令

现在，你可以创建一个命令。 让我们使用一个示例，该示例将使用单个查询文本、`turn on the tv`，并使用 `Ok, turning on the TV`的消息进行响应。

1. 通过选择 "命令" 旁边的 "`+`" 图标并为其指定名称来创建新命令 `TurnOn`
1. 选择“保存”

> [!div class="mx-imgBorder"]
> ![创建命令](media/custom-speech-commands/create-add-command.png)

命令是一组：

| 组            | 说明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 例句 | 示例最谈话用户可以说触发此命令                                                                 |
| Parameters       | 完成命令所需的信息                                                                                |
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

> [!div class="mx-imgBorder"]
> ![创建完成规则](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 设置    | 建议的值                        | 说明                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| 规则名称  | "ConfirmationResponse"                 | 描述规则用途的名称          |
| 条件 | 无                                   | 确定何时可以运行规则的条件    |
| 操作    | SpeechResponse "确定，打开电视" | 规则条件为 true 时要执行的操作 |

## <a name="try-it-out"></a>试用

使用 "测试聊天" 面板测试行为。

> [!div class="mx-imgBorder"]
> ![测试与 webchat](media/custom-speech-commands/create-basic-test-chat.png)

- 键入 "打开电视"
- 预期响应： "正常，打开电视"

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
