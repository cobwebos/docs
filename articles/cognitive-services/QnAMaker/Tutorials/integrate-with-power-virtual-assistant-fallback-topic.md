---
title: 教程：与 Power Virtual Agents 集成 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 本教程介绍如何使用主动学习改善知识库的质量。 在不删除或更改现有问题的情况下检查、接受或拒绝以及添加问题。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402817"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>教程：将知识库添加到 Power Virtual Agents
创建和扩展 [Power Virtual Agents](https://powervirtualagents.microsoft.com/) 机器人，以从知识库提供答案。

在本教程中，你将了解如何执行以下操作：

<!-- green checkmark -->
> [!div class="checklist"]
> * 创建 Power Virtual Agents 机器人
> * 创建系统回退主题
> * 将 QnA Maker 作为操作添加到作为 Power Automate 流的主题
> * 创建 Power Automate 解决方案
> * 将 Power Automate 流添加到解决方案
> * 发布 Power Virtual Agents
> * 测试 Power Virtual Agents，接收来自 QnA Maker 知识库的答案

## <a name="integrate-an-agent-with-a-knowledge-base"></a>将代理与知识库集成

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) 可让团队使用引导式的无代码图形界面轻松创建强大的机器人。 无需求助于数据科学家或开发人员。

在 Power Virtual Agents 中，可以使用一系列主题（主题区域）创建一个代理，以通过执行操作来回答用户的问题。 如果找不到答案，系统回退可以返回答案。

可以配置该代理，以将问题作为主题操作的一部分或者作为“系统回退”主题路径的一部分发送到知识库。  两者都使用操作连接到知识库并返回答案。

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate 连接到 `GenerateAnswer` 操作

若要将代理连接到知识库，请使用 Power Automate 来创建操作。 Power Automate 提供一个过程流，该流连接到 QnA Maker 的 `GenerateAnswer` API。

设计并保存流后，可以通过 Power Automate 解决方案使用该流。 使用该解决方案作为代理中的操作。

## <a name="connect-an-agent-to-your-knowledge-base"></a>将代理连接到知识库

下面概述了将 Power Virtual Agents 中的代理连接到 QnA Maker 中的知识库的步骤。

* 在 [QnA Maker](https://www.qnamaker.ai/) 门户中：
    * 生成并发布知识库。
    * 复制知识库详细信息，包括 ID、运行时终结点密钥和运行时终结点主机。
* 在 [Power Virtual Agents](https://powerva.microsoft.com/) 门户中：
    * 生成代理主题。
    * （对 Power Automate 流）调用操作。
* 在 [Power Automate](https://us.flow.microsoft.com/) 门户中：
    * 生成包含 [QnA Maker GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/) 连接器的流。
        * QnA Maker 发布的知识库信息：
            * 知识库 ID
            * QnA Maker 资源终结点主机
            * QnA Maker 资源终结点密钥
        * 输入 - 用户查询
        * 输出 - 知识库答案
    * 创建解决方案并添加流。
* 返回到 Power Virtual Agents：
    * 选择解决方案的输出作为主题的消息。

## <a name="create-and-publish-a-knowledge-base"></a>创建并发布知识库

1. 遵循[快速入门](../Quickstarts/create-publish-knowledge-base.md)创建一个知识库。 请不要完成有关创建机器人的最后一个部分。 请改用本教程通过 Power Virtual Agents 创建一个机器人。

    > [!div class="mx-imgBorder"]
    > ![发布的知识库设置的屏幕截图](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    输入在 [QnA Maker](https://www.qnamaker.ai/) 门户中的“设置”页上找到的已发布知识库设置。  在执行 [Power Automate 步骤](#create-a-power-automate-flow-to-connect-to-your-knowledge-base)来配置 QnA Maker `GenerateAnswer` 连接时，需要使用此信息。

1. 在 QnA Maker 门户中的“设置”页上，找到终结点密钥、终结点主机和知识库 ID。 

## <a name="create-an-agent-in-power-virtual-agents"></a>在 Power Virtual Agents 中创建代理

1. [登录到 Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409)。 使用你的学校或工作电子邮件帐户。
1. 如果这是你的第一个机器人，应会看到该代理的“主页”。  如果这不是你的第一个机器人，请在页面的右上区域中选择该机器人，然后选择“+ 新建机器人”。 

    > [!div class="mx-imgBorder"]
    > ![Power Virtual Agents 主页的屏幕截图](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. 输入在 [QnA Maker](https://www.qnamaker.ai/) 门户中的“设置”页上找到的已发布知识库设置。 

## <a name="topics-provided-in-the-bot"></a>机器人中提供的主题

代理将使用主题集合来回答主题区域中的问题。 在本教程中，代理已为你提供了许多主题，这些主题划分为用户主题和系统主题。

> [!div class="mx-imgBorder"]
> ![代理中提供的主题的屏幕截图](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>创建系统回退主题

虽然代理可以从任何主题连接到知识库，但本教程使用的是系统回退主题。  如果代理找不到答案，则会使用回退主题。 代理将用户的文本传递给 QnA Maker 的 `GenerateAnswer` API，从知识库接收答案，然后将答案作为消息显示给用户。

1. 在 [Power Virtual Agents](https://powerva.microsoft.com/#/) 门户的右上角，选择“设置”（齿轮图标）。  然后选择“系统回退”。 

    > [!div class="mx-imgBorder"]
    > ![用于“系统回退”的 Power Virtual Agents 菜单项的屏幕截图](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. 选择“+ 添加”以添加系统回退主题。 

    > [!div class="mx-imgBorder"]
    > ![添加回退主题的屏幕截图。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. 添加主题后，选择“转到回退主题”以在创作画布上创作回退主题。 

    > [!TIP]
    > 如果需要返回到回退主题，可以在“主题”部分中找到它，它作为“系统”主题的一部分列出。  

## <a name="use-the-authoring-canvas-to-add-an-action"></a>使用创作画布添加操作

使用 Power Virtual Agents 创作画布将回退主题连接到知识库。 该主题以不可识别的用户文本开头。 添加一个操作，用于将该文本传递给 QnA Maker，然后将答案显示为消息。 显示答案的最后一个步骤将作为[单独的步骤](#add-your-solutions-flow-to-power-virtual-agents)进行处理，本教程稍后将予以介绍。

本部分将创建回退主题对话流。

1. 新的回退操作可能已包含对话流元素。 通过选择“选项”菜单来删除“提升”项。  

    > [!div class="mx-imgBorder"]
    > ![Power Virtual Agents 回退主题的屏幕截图](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. 在“消息”框中选择“+”连接器流，然后选择“调用操作”。   

    > [!div class="mx-imgBorder"]
    > ![“调用操作”的屏幕截图](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. 选择“创建流”。  该过程会将你转到 Power Automate 门户。

    > [!div class="mx-imgBorder"]
    > ![“创建流”的屏幕截图](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>创建 Power Automate 流以连接到知识库

以下过程创建一个 Power Automate 流，该流可以：
* 提取传入的用户文本，并将其发送到 QnA Maker。
* 将 QnA Maker 最佳答案分配到某个变量，并将该变量（最佳答案）作为响应发回给代理。

1. 在 Power Automate 中，将为你启动“流模板”。   在“Power Virtual Agents”流项中，选择“编辑”以配置从代理发送到知识库的输入变量。   该基于文本的输入变量是用户从代理提交的文本问题。

    > [!div class="mx-imgBorder"]
    > ![用于将输入变量配置为文本字符串的 Power Automate 选项屏幕截图](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. 添加一个文本输入，将变量命名为 `InputText`，并提供说明 `IncomingUserQuestion`。 这种命名有助于将输入文本与稍后要创建的输出文本区分开来。

    > [!div class="mx-imgBorder"]
    > ![用于配置输入变量名称和说明的 Power Automate 选项屏幕截图](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. 在“Power Virtual Agents”框中选择“+”连接器流，以在流中插入新步骤（在“将值返回到 Power Virtual Agents”的前面）。    然后选择“添加操作”。 

1. 搜索 `Qna` 找到“QnA Maker”操作，然后选择“生成答案”。  

    > [!div class="mx-imgBorder"]
    > ![“生成答案”的屏幕截图](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    QnA Maker 所需的连接设置显示在代理的操作和问题设置中。

    > [!div class="mx-imgBorder"]
    > ![所需连接设置的屏幕截图](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. 使用知识库 ID、终结点主机和终结点密钥配置该操作。 可在 QnA Maker 门户中知识库的“设置”页上找到这些信息。 

    > [!div class="mx-imgBorder"]
    > ![发布的知识库设置的屏幕截图](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. 若要配置“问题”，请选择文本框，然后从列表中选择 `InputText`。 

1. 若要在流中插入新步骤，请在“生成答案”操作框中选择“+”连接器流。   然后选择“添加操作”。 

1. 若要添加一个变量来捕获从 `GenerateAnswer` 返回的答案文本，请搜索并选择 `Initialize variable` 操作。

    将变量名称设置为 `OutgoingQnAAnswer`，并选择“字符串”作为类型。  不要设置“值”。 

    > [!div class="mx-imgBorder"]
    > ![初始化输出变量的屏幕截图](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. 若要在流中插入新步骤，请在“初始化变量”操作框中选择“+”连接器流。   然后选择“添加操作”。 

1. 若要将整个知识库 JSON 响应设置为变量，请搜索并选择 `Apply to each` 操作。 选择 `GenerateAnswer` `answers`。

1. 若要仅返回最佳答案，请在同一个“应用于每一项”框中，选择“添加操作”。   搜索并选择“设置变量”。 

    在“设置变量”框中，选择“名称”对应的文本框，然后从列表中选择“OutgoingQnAAnswer”。   

    选择“值”对应的文本框，然后从列表中选择“答案”。  

    > [!div class="mx-imgBorder"]
    > ![为变量设置名称和值的屏幕截图](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. 若要返回变量（及其值），请选择“将值返回到 Power Virtual Agents”流项。  然后选择“编辑” > “添加输出”。   选择“文本”输出类型，然后在“标题”中输入 `FinalAnswer`。   选择“值”对应的文本框，然后选择 `OutgoingQnAAnswer` 变量。 

    > [!div class="mx-imgBorder"]
    > ![设置返回值的屏幕截图](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. 选择“保存”以保存流  。

## <a name="create-a-solution-and-add-the-flow"></a>创建解决方案并添加流

要使代理能够找到并连接到流，必须将该流包含在 Power Automate 解决方案中。

1. 在仍处于 Power Automate 门户中的情况下，从左侧导航栏中选择“解决方案”。 

1. 选择“+ 新建解决方案”。 

1. 输入显示名称。 解决方案列表包含你的组织或学校中的每个解决方案。 选择一种命名约定帮助进行筛选，以便仅列出你的解决方案。 例如，可以使用你的电子邮件名作为解决方案名称的前缀：`jondoe-power-virtual-agent-qnamaker-fallback`。

1. 从选项列表中选择发布者。

1. 对于名称和版本，请接受默认值。

1. 选择“创建”以完成该过程。 

## <a name="add-your-flow-to-the-solution"></a>将流添加到解决方案

1. 在解决方案列表中，选择刚刚创建的解决方案。 该解决方案应位于列表的顶部。 否则，请通过电子邮件名称（解决方案名称的一部分）进行搜索。

1. 在该解决方案中，选择“+ 添加现有项”，然后从列表中选择“流”。  

1. 找到你的流，然后选择“添加”以完成该过程。  如果有很多流，请查看“修改时间”列以找到最近修改的流。 

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>将解决方案的流添加到 Power Virtual Agents

1. 返回到包含 Power Virtual Agents 中的代理的浏览器标签页。 创作画布应仍处于打开状态。

1. 若要在流中插入新步骤，请选择“消息”操作框下的“+”连接器。   然后选择“调用操作”。 

1. 在新操作中，选择“UnrecognizedTriggerPhrase”输入值。  这会将来自代理的文本传递给该流。

    > [!div class="mx-imgBorder"]
    > ![用于选择不可识别的触发器短语的 Power Virtual Agents 选项屏幕截图](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. 若要在流中插入新步骤，请选择“操作”框下的“+”连接器。   然后选择“显示消息”。 

1. 输入消息文本 `Your answer is:`。 使用就地提供的工具栏的功能来选择 `FinalAnswer` 作为上下文变量。

    > [!div class="mx-imgBorder"]
    > ![用于输入消息文本的 Power Virtual Agents 选项屏幕截图](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. 在上下文工具栏中选择“保存”，以保存主题的创作画布详细信息。 

最终的代理画布如下所示。

> [!div class="mx-imgBorder"]
> ![最终代理画布的屏幕截图](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>测试代理

1. 在测试窗格中，开启“在主题之间跟踪”。  这样，就可以观察主题之间的以及单个主题内部的处理进度。

1. 按以下顺序输入用户文本，以测试代理。 创作画布通过绿色的对号标记来报告成功的步骤。

    |问题顺序|测试问题|目的|
    |--|--|--|
    |1|你好|开始对话|
    |2|商店营业时间|示例主题。 这已为你配置好了，不需要你做任何额外的工作。|
    |3|是|回复 `Did that answer your question?`|
    |4|很好|回复 `Please rate your experience.`|
    |5|是|回复 `Can I help with anything else?`|
    |6|知识库是什么？|此问题会触发回退操作，而该操作会将文本发送到知识库以提供答案。 然后会显示答案。 |

> [!div class="mx-imgBorder"]
> ![最终代理画布的屏幕截图](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>发布机器人

要使代理可供学校或组织中的所有成员使用，需要发布该代理。

1. 在左侧导航栏中选择“发布”。  然后在页面上选择“发布”。 

1. 在演示网站（在“发布”下查找链接）上尝试运行机器人。 

    此时会打开包含你的机器人的新网页。 向机器人提出相同的测试问题：`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![最终代理画布的屏幕截图](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>共享机器人

若要共享演示网站，请将其配置为频道。

1. 在左侧导航栏中，选择“管理” > “频道”。  

1. 在频道列表中选择“演示网站”。 

1. 复制链接并选择“保存”。  将演示网站的链接粘贴到要发送给学校或组织成员的电子邮件中。

## <a name="clean-up-resources"></a>清理资源

用完知识库后，请在 Azure 门户中删除 QnA Maker 资源。

## <a name="next-step"></a>后续步骤

[获取有关知识库的分析](../How-To/get-analytics-knowledge-base.md)

了解有关以下方面的详细信息：
* [Power Virtual Agent](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker 连接器](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/)和[连接器设置](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)