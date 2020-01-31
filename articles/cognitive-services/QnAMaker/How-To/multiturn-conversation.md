---
title: 多轮会话-QnA Maker
titleSuffix: Azure Cognitive Services
description: 使用 "提示" 和 "上下文" 来管理机器人，使机器人从一个问题到另一个问题。 多轮功能可以让您在上一个问题的上下文中影响下一个问题和答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 681f00adde1d440fbb41f2e66652db34d53fbf99
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843254"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>使用跟进提示创建多个轮次的对话

使用 "后续提示" 和 "上下文" 来管理_机器人，使_机器人从一个问题到另一个问题。

若要查看多轮工作原理，请查看以下演示视频：

[QnA Maker 中的 ![多轮会话](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>什么是多轮对话？

不能在单次轮中回答某些问题。 在设计客户端应用程序（聊天机器人）对话时，用户可能会询问需要筛选或改进的问题，以确定正确的答案。 通过向用户提供*跟进提示*，你可以使此流出现问题。

当用户提出问题时，QnA Maker 将返回应答_和_任何后续提示。 此响应允许您将后续问题作为选择提供。

## <a name="example-multi-turn-conversation-with-chat-bot"></a>带聊天机器人的多轮会话示例

通过多轮功能，聊天机器人可管理与用户的会话，以确定最终答案，如下图所示：

![具有指导用户完成对话的提示的多轮对话框](../media/conversational-context/conversation-in-bot.png)

在上图中，用户通过输入**我的帐户**开始了一个会话。 该知识库具有三个链接的问题和答案对。 若要优化答案，用户需要选择知识库中的三个选项之一。 问题（#1）有三个后续提示，这些提示在聊天机器人中显示为三个选项（#2）。

当用户选择某个选项（#3）时，将显示下一组优化选项（#4）。 此顺序将继续（#5），直到用户确定正确的最后答案（#6）。


### <a name="use-multi-turn-in-a-bot"></a>在机器人中使用多个

发布知识库后，可以选择 "**创建 Bot** " 按钮，将 QnA Maker 机器人部署到 Azure bot 服务。 系统将在你为机器人启用的聊天客户端中显示提示。

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>从文档的结构创建多轮对话

当你创建知识库时，"**填充你的 KB** " 部分将显示 "**从 url、.pdf 或 .docx 文件启用多项提取"** 复选框。

![启用多轮提取的复选框](../media/conversational-context/enable-multi-turn.png)

如果选择此选项，QnA Maker 将提取文档结构中存在的层次结构。 在中转换层次结构以跟踪提示，并将层次结构的根用作父 QnA。 在某些文档中，层次结构的根不具有可用作答案的内容，你可以提供 "默认答案文本" 作为替代文本来提取此类层次结构。

只能从 Url、PDF 文件或 .DOCX 文件推断出多个结构。 有关结构的示例，请查看[Microsoft Surface user 手动 PDF 文件](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)的图像。

![![用户手册中的结构示例](../media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>构建自己的多文档

若要创建多个文档，请记住以下准则：

* 使用标题和子标题来表示层次结构。 例如，可以通过 h1 表示父 QnA 和 h2，以表示应作为 prompt 出现的 QnA。 使用较小的标题尺寸来表示后续层次结构。 不要使用样式、颜色或其他某些机制来表示文档中的结构，QnA Maker 将不会提取多个提示。

* 标题的第一个字符必须大写。

* 不要以问号结束标题，`?`。

* 您可以使用[示例文档](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)作为示例来创建自己的多文档。

### <a name="adding-files-to-a-multi-turn-kb"></a>将文件添加到多个 KB

添加分层文档时，QnA Maker 会确定结构中的后续提示，以创建对话流。

1. 在 QnA Maker 中，选择使用 "**从 url、pdf 或 .docx 文件中启用多个提取"** 创建的现有知识库。 能够.
1. 请在 "**设置**" 页上，选择要添加的文件或 URL。
1. **保存并训练**知识库。

> [!Caution]
> 不支持使用导出的 TSV 或 XLS 多转换知识库文件作为新的或空知识库的数据源。 你需要在 QnA Maker 门户的 "**设置**" 页中**导入**该文件类型，以便将导出的 "多提示" 提示添加到知识库中。


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>通过创建 API 创建包含多个提示的知识库

您可以使用[QnA Maker 创建 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)创建包含多个提示的知识案例。 在 `context` 属性的 `prompts` 数组中添加提示。

## <a name="show-questions-and-answers-with-context"></a>用上下文显示问题和答案

将显示的问题和答案对减少到仅具有上下文对话的问题。

选择 "**查看选项**"，然后选择 "**显示上下文**"。 列表将显示包含后续提示的问题和答案对。

![按上下文对话筛选问题和答案对](../media/conversational-context/filter-question-and-answers-by-context.png)

多转换上下文显示在第一列中。

![!["上下文（预览）" 列](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

在上图中， **#1**指示列中的粗体文本，这表示当前问题。 父问题是行中的顶级项。 下面的任何问题都是链接的问答对。 可以选择这些项，以便您可以立即跳到其他上下文项。

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>添加现有问题和答案对作为跟进提示

最初的问题 "**我的帐户**" 具有后续提示，例如**帐户和登录**。

!["帐户和登录" 的答案和跟进提示](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

向当前未链接的现有答案对添加跟进提示。 由于问题未链接到任何问题和答案对，因此需要更改当前视图设置。

1. 若要将现有的问题和答案对链接为后续提示，请选择问题和答案对对应的行。 对于表面手册，搜索 "**注销**" 以减少列表。
1. 在**Signout**的行中的 "**答案**" 列中，选择 "**添加后续提示**"。
1. 在 "**跟进提示符**" 弹出窗口的 "字段" 中，输入以下值：

    |字段|值|
    |--|--|
    |显示文本|输入 **"关闭设备"** 。 这是要在后续提示中显示的自定义文本。|
    |仅限上下文| 选中此复选框。 仅当问题指定上下文时，才返回答案。|
    |链接到答案|输入 "**使用登录屏幕**查找现有的问题和答案对"。|


1.  返回一个匹配项。 选择此应答作为跟进，然后选择 "**保存**"。

    !["后续提示（预览）" 页](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 添加后续提示后，在顶部导航栏中选择 "**保存并训练**"。

### <a name="edit-the-display-text"></a>编辑显示文本

当创建了跟进提示，并输入了现有的问题和答案对作为**应答的链接**时，您可以输入新的**显示文本**。 此文本不会替换现有问题，也不会添加新的替代问题。 它与这些值不同。

1. 若要编辑显示文本，请在**上下文**字段中搜索并选择问题。
1. 在该问题所在的行中，选择 "答案" 列中的 "跟进" 提示。
1. 选择要编辑的显示文本，然后选择 "**编辑**"。

    ![用于显示文本的 "编辑" 命令](../media/conversational-context/edit-existing-display-text.png)

1. 在 "**跟进提示符**" 弹出窗口中，更改现有的显示文本。
1. 编辑完显示文本后，请选择 "**保存**"。
1. 在顶部导航栏中，**保存并训练**。


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>添加新的问题和答案对作为跟进提示

在将新的问题与答案对添加到知识库中时，每个对都应链接到一个现有问题作为跟进提示。

1. 在知识库工具栏上，搜索并选择帐户的现有问答对**并登录**。

1. 在此问题的 "**答案**" 列中，选择 "**添加后续提示**"。
1. 在 "**后续提示（预览）** " 下，输入以下值创建新的后续提示：

    |字段|值|
    |--|--|
    |显示文本|*创建 Windows 帐户*。 要在跟进提示中显示的自定义文本。|
    |仅限上下文|选中此复选框。 仅当问题指定了上下文时，才返回此答案。|
    |链接到答案|输入以下文本作为答案：<br>*使用新的或现有的电子邮件帐户[创建](https://account.microsoft.com/)Windows 帐户*。<br>保存并训练数据库时，将转换此文本。 |
    |||

    ![创建新的提示问题和答案](../media/conversational-context/create-child-prompt-from-parent.png)


1. 选择 "**新建**"，然后选择 "**保存**"。

    此操作创建新的问题和答案对，并将所选问题链接为后续提示。 对于这两个问题，**上下文**列都表示跟进提示符关系。

1. 选择 "**查看选项**"，然后选择 "[**显示上下文（预览）** ](#show-questions-and-answers-with-context)"。

    新问题显示了其链接方式。

    ![创建新的后续提示](../media/conversational-context/new-qna-follow-up-prompt.png)

    父问题将新问题显示为它的一个选择。

    ![![这两个问题的 "上下文" 列表示跟进提示符关系](../media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 添加后续提示后，在顶部导航栏中选择 "**保存并训练**"。

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>在测试跟进提示期间启用多轮

当你在**测试**窗格中使用跟进提示测试问题时，请选择 "**启用多轮**"，然后输入你的问题。 响应包括跟进提示。

![响应包括跟进提示](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

如果不启用多重转换，则返回答案，但不返回跟进提示。

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>返回初始答案和跟进提示的 JSON 请求

使用空的 `context` 对象可以请求用户的问题的答案，并包括跟进提示。

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>返回初始答案和跟进提示的 JSON 响应

前面的部分请求了有关**帐户和登录**的答案和后续提示。 响应包括提示信息，该信息位于*答案为 [0]。上下文*，以及向用户显示的文本。

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts` 数组在 `displayText` 属性和 `qnaId` 值中提供文本。 您可以将这些答案显示为会话流中的下一个显示选项，然后将所选 `qnaId` 发送回 QnA Maker 在以下请求中。

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>返回非初始应答和跟进提示的 JSON 请求

填充 `context` 对象以包括上一个上下文。

在下面的 JSON 请求中，当前问题是*使用 Windows Hello 登录*，而以前的问题是*帐户并登录*。

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>返回非初始应答和跟进提示的 JSON 响应

QnA Maker _GenerateAnswer_ JSON 响应包括 `answers` 对象中第一项的 `context` 属性中的后续提示：

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>查询具有 QnA Maker ID 的知识库

如果要使用多项功能构建自定义应用程序。 在初始问题的响应中，会返回任何后续提示及其关联 `qnaId`。 使用 ID 后，可以在后续提示的请求正文中传递此 ID。 如果请求正文包含 `qnaId`和上下文对象（其中包含之前的 QnA Maker 属性），则 GenerateAnswer 将按 ID 返回准确的问题，而不是使用排名算法来通过问题文本查找答案。


## <a name="display-order-is-supported-in-the-update-api"></a>更新 API 支持显示顺序

[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)支持在 JSON 响应中返回的[显示文本和显示顺序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)。

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>添加或删除带有更新 API 的多个提示

你可以使用[QnA Maker 更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)添加或删除多个提示。  在 `context` 属性的 `promptsToAdd` 数组和 `promptsToDelete` 数组中添加提示。

## <a name="export-knowledge-base-for-version-control"></a>用于版本控制的导出知识库

QnA Maker 通过在导出文件中包含多轮会话步骤，支持版本控制。

## <a name="next-steps"></a>后续步骤

详细了解此[对话示例](https://aka.ms/qnamakermultiturnsample)中的上下文对话，或了解有关[多个会话的概念 bot 设计的](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)详细信息。

> [!div class="nextstepaction"]
> [迁移知识库](../Tutorials/migrate-knowledge-base.md)
