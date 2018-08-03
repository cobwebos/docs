---
title: 如何将对话学习器与其他机器人构建技术结合使用 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何将对话学习器与其他机器人构建技术结合使用。
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: a03596ff8383a085314508d4a25d0ba89bcc3094
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174476"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>如何将对话学习器与其他机器人构建技术结合使用

本教程介绍如何将对话学习器与其他机器人构建技术结合使用，以及如何在这些技术之间共享内存（或状态） 

## <a name="video"></a>视频

[![教程 15 预览](http://aka.ms/cl-tutorial-15-preview)](http://aka.ms/blis-tutorial-15)

## <a name="requirements"></a>要求
本教程需要使用机器人模拟器（而非记录对话 Web UI）来创建记录对话。  

本教程要求运行混合教程机器人：

    npm run tutorial-hybrid

## <a name="details"></a>详细信息

控制对话学习器时，与对话学习器会话相关的所有状态必须都存储在对话学习器的内存管理器中。 这一过程非常重要，因为机器学习使用状态来确定如何推动对话。 外部状态可以传入会话开始时调用的 OnSessionStartCallback 中的对话学习器。 会话终止时，OnSessionEndCallback 可以返回内部。

对话学习器几乎可以视作一个接受某种初始状态并返回值的函数调用。

在此示例中，将使用两个不同的系统创建混合机器人：
1. 对话学习器模型 <br />
使用对话学习器模型以根据当前会话确定机器人的下一个动作。
机器人的这一部分采用初始状态 `isOpen`（指示商店是营业还是打烊）并返回另一个状态 `purchaseItem`（用户购买的商品的名称）。

2. 文本匹配 <br />
只需查看特定字符串的传入文本并做出响应。
机器人的这一部分管理机器人的其他存储机制，并负责启动 CL 会话。 具体而言，它管理三个变量：`usingConversationLearner`、`storeIsOpen` 和 `purchaseItem`。

我们先来看看此演示中使用的模型。

### <a name="open-the-demo"></a>打开演示

在 Web UI 的“模型”列表中，单击“Tutorial-15-Hybrid”。

## <a name="entities"></a>实体

打开实体页，可以看到两个实体：`isOpen` 和 `purchaseItem`

要了解如何使用这些实体，请打开 `C:\<installedpath\>\src\demos\tutorialHybrid.ts` 文件查看回调。

请注意，`OnSessionStartCallback` 中的代码将从 BotBuilder 会话存储复制 `storeIsOpen` 的值作为 `isOpen` 实体的值，以供对话学习器使用。 请参阅以下代码：

![](../media/tutorial17_sessionstart.PNG)

同样，`OnSessionEndCallback` 中的代码（如果会话由于某个已学习的活动而不仅仅是因为超时而结束）会将实体 `purchaseItem` 的值复制到 BotBuilder 存储 `purchaseItem`。 请参阅以下代码：

![](../media/tutorial17_sessionend.PNG)

现在，让我们来看一下这些操作。

## <a name="actions"></a>操作

注意该模型有四个操作。

这些操作的预期规则如下：

- 如果设置了 `isOpen` 实体，机器人会问：“What would you like to buy?” 并将其存储在 `puchaseItem` 槽中
- 如果未设置 `isOpen`，机器人会说：“I’m sorry we’re closed”
- 其他两个操作都属于 `END_SESSION` 类型。
- END_SESSION 操作会指示对话学习器已完成会话。

### <a name="overall-bot-logic"></a>整体的机器人逻辑

首先，如果发现设置了机器人状态的 `usingConversationLearner` 标记，我们会将控制权传递给对话学习器。 如果没有，则将控制权传递给其他程序。  本例中演示了简单的文本匹配，但这可能是任何其他机器人技术，包括 LUIS、QnA Maker，甚至是对话学习器的另一个实例。

我们需要为用户提供营业和打烊的方法，因此将比较“open store”和“close store”字符串并设置“storeIsOpen”标志。

接下来，我们需要一种方法来触发将控制权移交给学习器模型的过程。 匹配“shop”字符串时，执行以下操作：
- 在机器人的内存中设置 `usingConversationLearner` 标志。
- 在对话学习器模型上调用“StartSession”方法。  这会触发"onSessionStartCallback"（将初始化 `isOpen` 实体值）

请参见下图：

![](../media/tutorial17_useConversationLearner.PNG)

我们还对“历史记录”进行了文本匹配，这样会显示最近购买的商品。
最后，如果键入了任何其他内容，则会显示可用的用户命令

## <a name="train-dialog"></a>训练对话

本教程已预先定型模型。  我们将测试完整的机器人，在实践中查看开始和结束会话回调的效果。

## <a name="testing-the-bot"></a>测试机器人

不同于单个对话学习器模型机器人，你无法在对话学习器 UI 中对其进行测试，因为它只能显示对话学习器模型所处理的内容。

### <a name="install-the-bot-framework-emulator"></a>安装 Bot Framework Emulator

- 转到[https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)。
- 下载并安装模拟器。

### <a name="configure-the-emulator"></a>配置模拟器

- 打开模拟器，确保 URL 定向到机器人运行所在的同一端口。 可能为：`http://localhost:3978/api/messages`

### <a name="test"></a>测试 

#### <a name="scenario-1-store-is-closed"></a>方案 1：商店已打烊
1. 输入“shop”。 这由文本匹配处理，会将控制权交给对话学习器模型。
2. 输入“hello”。  由于未设置 `isOpen` 值，机器人会说：“I’m sorry we’re closed”，然后关闭会话。

#### <a name="scenario-2-store-is-open"></a>方案 2：商店已营业
3. 输入“open store”。  这会将 `isOpen` 设置为 true。
4. 输入“shop”。
5. 输入“hello”。  由于 `isOpen` 实体设置为 true，机器人会说：“What would you like to buy?”
6. 输入“chair”。 “chair”将在 CL 内存中保存为 `purchaseItem` 实体。 调用的结束会话回调会将此值复制到会话存储中。
7. 输入“history”。  机器人会说：“'You bought chair”（因为这是你最近一次购买的商品）`purchaseItem`。

## <a name="conclusion"></a>结束语

根据前面所学的知识，你应能够将对话学习器与任何其他机器人技术相结合
