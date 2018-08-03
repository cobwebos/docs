---
title: 如何通过对话学习器模型使用版本控制和标记 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用版本控制和标记。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c7f23d989cbfa0ece9e404a0fe0feb68cf5fddb2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170539"
---
# <a name="how-to-use-versioning-and-tagging"></a>如何使用版本控制和标记

本教程说明了如何标记对话学习器模型的版本，并设置哪个版本是“活动的”。  

## <a name="requirements"></a>要求
本教程需要使用机器人模拟器（而非记录对话 Web UI）来创建记录对话。  

本教程要求运行常规教程机器人：

    npm run tutorial-general

## <a name="details"></a>详细信息

编辑时，你始终在编辑名为“master”的标记 - 你可以从 master 创建标记版本（实质上是创建 master 的快照），但你无法编辑标记版本。

## <a name="steps"></a>Steps

### <a name="install-the-bot-framework-emulator"></a>安装 Bot Framework Emulator

- 转到[https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)。
- 下载并安装模拟器。

### <a name="create-an-model"></a>创建模型

1. 单击“新建模型”
2. 在“名称”字段中，输入“Tutorial-16-Versioning”
3. 单击创建 
4. 单击“设置”
5. 复制模型 ID

### <a name="configure-the-emulator"></a>配置模拟器

- 在对话学习器根文件夹中，打开 .env 文件。
- 将模型 ID 粘贴为 CONVERSATION_LEARNER_MODEL_ID 的值
- 通过退出命令提示符并重新运行以下命令重启对话学习器服务：
 
    npm run tutorial-general 

### <a name="actions"></a>操作

让我们创建一个操作：

1. 切换到 Web UI。
1. 依次单击“操作”和“新建操作”。
2. 在“响应”中，输入“hi there (version 1)”。
3. 单击“保存”。


![](../media/tutorial16_action1.PNG)

新建标记：

3. 单击“设置”并创建新的“标记”。
    - 称之为“version 1”
4. 将“version 1”设置为“活动”。  
    - 将活动标记设置到“version 1”的效果是使用此机器人的通道将使用“version 1”标记。
    - 带标记的模型版本不受编辑（更改操作、实体，添加定型对话）的影响。  
    - 对模型的编辑（更改操作、实体，添加定型对话）始终在“master”标记上进行。  换句话说，“master”是唯一可更改的标记；其他标记是固定快照。
    - 对话学习器 UI 中的记录对话始终使用 master（而不是活动标记）。

![](../media/tutorial16_v1_create.PNG)

该版本已在设置中创建：

![](../media/tutorial16_settings.PNG)

让我们添加第二个操作：

1. 依次单击“操作”和“新建操作”。
2. 在“响应”中，输入“bye bye (version 2)”。

编辑第一个操作：

1. 单击“操作”。
2. 在“操作”下，单击“hi there (version 1)”。
3. 将响应更改为“hi there (version 2)”。

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>切换到机器人模拟器

1. 在机器人 UI 中，输入“goodbye”。
2. 机器人会以“hi there (version 1)”来进行响应。
    - 这表明版本 1 是“活动的”。 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>切换到 Web UI

1. 单击“记录对话”（如果没有看到任何对话，请单击“刷新”按钮）。
2. 单击“hi there (version 2)”

> [!NOTE]
> 我们可以通过从所有当前可用的操作中进行选择来做出更正。 这些编辑将对 master 进行。

现在已了解版本控制的工作原理，以及如何使用 Bot Framework Emulator 与机器人进行交互。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [演示 - 密码重置](./demo-password-reset.md)
