---
title: 如何对 Conversation Learner 模型使用版本标记 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用版本控制和标记。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99b4712373423d30dc83408cc80eccc93b342c10
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205994"
---
# <a name="how-to-use-version-tagging"></a>如何使用版本标记

本教程说明如何标记 Conversation Learner 模型的版本，并设置哪个版本是“实时”的。  

## <a name="requirements"></a>要求
本教程要求使用 Bot Framework Emulator 创建日志对话，而非日志对话 Web UI。  

本教程要求常规教程机器人处于运行状态：

    npm run tutorial-general

## <a name="details"></a>详细信息

模型的标记版本是静态的；不能对其进行编辑或更改。 在编辑模型时，始终编辑的是主版本。 在添加新标记时，Conversation Learner 将在该时间点捕获模型的快照。 

机器人将使用已选为“实时”版本的模型版本，但是，仅当“编辑标记”设置为“主”时，才能查看该机器人所进行的任何聊天。 如果模型的“编辑标记”属性未设置为“主”，则只能查看该模型的快照，而不能以任何方式对其进行更改。

## <a name="steps"></a>Steps

### <a name="install-the-bot-framework-emulator"></a>安装 Bot Framework Emulator

1. 转到[https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)。
2. 下载并安装模拟器。

### <a name="create-a-model"></a>创建模型

1. 在“模型列表”主页中，单击 `New Model` 按钮。
2. 在 `Name` 字段中，键入“Tutorial-18-Versioning”，然后按 Enter。
4. 在左面板中，单击“设置”。
5. 将 CONVERSATION_LEARNER_MODEL_ID 字段的内容复制到剪贴板。

### <a name="configure-the-emulator"></a>配置 Emulator

1. 在 Conversation Learner 的根文件夹中，打开“.env”文件。
2. 向“.env”文件中添加一行，如下所示：
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. 通过退出命令提示符并重新运行以下命令重启对话学习器服务：
    - `npm run tutorial-general`
4. 在 Bot Framework Emulator 中，创建新的机器人配置，并将终结点 URL 设置为 `http://localhost:3978/api/messages`

### <a name="version-1"></a>版本 1

我们将为版本 1 创建单个操作。

1. 在 Web UI 的左面板中，单击“操作”，然后单击 `New Action` 按钮。
2. 在“机器人的响应”字段中，输入“hi there (version 1)”。
3. 单击“`Save`”按钮。

现在，我们就可以将此项标记为模型的“版本 1”了。

4. 在左面板中，单击“设置”，然后单击 ![](../media/tutorial18_version_tags.PNG)“版本标记”图标，以显示应单击的 `New Tag` 按钮。
    - 将其命名为“版本 1”
4. 在“实时标记”下拉列表中，选择“版本 1”。  
    - 现在，使用此机器人的通道将使用我们模型的“版本 1”。
    - 此版本 1 模型的实体、操作和训练对话不能再进行更改。
    - 如果将“版本 1”选为“编辑标记”，则只能查看模型，而不能对其进行编辑。
    - 保留“编辑标记”设置为“主”不变，这是可编辑的模型的唯一版本。

现在，“版本 1”将显示在“版本标记”网格中。

### <a name="version-2"></a>版本 2

现在，我们将编辑模型以将其与版本 1 区分开来。

1. 在左面板中，单击“操作”。
2. 在“操作”网格中，单击“hi there (version 1)”。
3. 将“机器人的响应”字段更改为“hi there (version 2)”。
4. 单击“`Save`”按钮。
5. 单击“`New Action`”按钮。
6. 在“机器人的响应”字段中，键入“bye bye (version 2)”。

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>确认 Bot Framework Emulator 使用的是版本 1

1. 在 Bot Framework Emulator 中，键入消息“Hey there”。
2. 请注意，机器人以“hi there (version 1)”做出响应。
    - 这将验证版本 1 是否是“实时”的。

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>在 Conversation Learner Web UI 中查看聊天日志

1. 在左面板中，单击“日志对话”
    - 如果未显示任何对话，请单击“刷新”按钮。
2. 请注意网格中的“版本 1”标记。
3. 在网格中，单击“hi there (version 1)”

> [!NOTE]
> 我们可通过从当前可用的所有 Conversation Learner 功能中进行选择来做出更正，但是，这些编辑操作将对“主”版本而非“版本 1”执行。

现在已了解了版本控制的工作原理，以及如何使用 Bot Framework Emulator 与机器人进行交互。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [演示 - 密码重置](./demo-password-reset.md)
