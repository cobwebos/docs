---
title: 多打开会话
titleSuffix: Azure Cognitive Services
description: 使用提示和上下文来管理多个将，称为多轮次，到另一个问题从智能机器人。 多轮次是已备份的能力相互间会话的前一个问题的上下文会影响下一步的问题和答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 3ca166b287858b3e42aeda1421d1733fe24c81ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479721"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>使用后续的提示操作以创建多个打开的会话

使用后续的提示和上下文来管理多个将，称为_多轮次_，到另一个问题从智能机器人。

观看下面的演示视频，若要查看如何执行操作。

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)：

## <a name="what-is-a-multi-turn-conversation"></a>什么是多轮次会话？

不能反过来单一回答一些问题。 在设计您的客户端应用程序 （聊天机器人） 对话时，用户可以询问一个问题就需要进行筛选和优化，以便确定正确的答案。 此流通过问题可通过提供与用户**跟进提示**。

当用户请求时的问题时，QnA Maker 返回答案_和_任何后续提示。 这可以提供为选择的后续问题。 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>与聊天机器人的示例多打开会话

聊天机器人管理用户，问题通过问题以确定最终答案的会话。

![中的对话式流，通过提供提示中显示为选项以继续此对话的答案管理多轮次对话框系统中会话状态。](../media/conversational-context/conversation-in-bot.png)

在上图中，用户输入`My account`。 知识库具有 3 个链接的 QnA 对。 用户需要从三个选项以优化答案之一中进行选择。 中的知识，在问题 (#1) 包含三个后续的提示，显示在聊天机器人为三个选项 (#2)。 

当用户选择一个选择 (#3) 时，然后显示下一个列表中的优化选项 (#4)。 这可以继续 (#5)，直到确定正确的和最终答案 (6)。

上图中有**启用多轮次**为了显示提示选择。 

### <a name="using-multi-turn-in-a-bot"></a>在智能机器人应用程序中使用多轮

您需要更改你的客户端应用程序管理上下文的会话。 将需要添加[到智能机器人代码](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting)若要显示的提示。  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>从文档的结构创建多打开会话

当您创建某一知识库时，您将看到可选复选框以启用多轮次提取。 

![当您创建某一知识库时，您将看到可选复选框以启用多轮次提取。](../media/conversational-context/enable-multi-turn.png)

如果选择此选项，导入文档时，可以从结构隐式多打开会话。 如果存在该结构，QnA Maker 将为你创建的后续提示 QnA 对。 

仅可以从 Url、 PDF 或 DOCX 推断出多轮次结构文件。 

下图的 Microsoft Surface [PDF 文件](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)旨在用作手动。 由于此 PDF 文件的大小，Azure QnA Maker 资源需要搜索定价层的 B （15 个索引） 或更高版本。 

![![如果导入文档时，上下文会话可以是隐式从结构。 如果存在该结构，QnA Maker 的后续提示 QnA 对为用户创建，作为文档导入的一部分。](../ media/conversational-context/import-file-with-conversational-structure.png）](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

在导入 PDF 文档，QnA Maker 确定从该结构以创建对话式流的后续提示。 

1. 在中**步骤 1**，选择**创建知识库**从顶部导航栏。
1. 在中**步骤 2**、 创建或使用现有的 QnA 服务。 请确保使用的搜索服务为 B QnA 服务 （15 个索引） 或更高，因为面手动 PDF 文件太大，更小级别。
1. 在中**第 3 步**，输入在知识库的名称，例如`Surface manual`。
1. 在中**步骤 4**，选择**启用多轮次提取从 Url、.pdf 或.docx 文件。** 图面上手动选择 URL

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. 选择**创建知识库**按钮。 

    创建知识库后，显示的问题和答案对视图。

## <a name="show-questions-and-answers-with-context"></a>显示问题和答案与上下文

减少到只是那些具备上下文会话显示的问题和答案对。 

1. 选择**查看选项**，然后选择**显示上下文 （预览版）** 。 此列表显示问题和答案对包含后续提示。 

    ![筛选的问题和回答上下文对话对](../media/conversational-context/filter-question-and-answers-by-context.png)

2. 第一列中显示多轮次上下文。

    ![![在导入 PDF 文档，QnA Maker 确定从该结构以创建对话式流的后续提示。 ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    在上图中，#1 指示粗体文本中的列，表示当前的问题。 父问题是一行中的顶级项。 以下任何问题是链接的问题和答案对。 这些项是可选的因此可以立即转到其他上下文项。 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>将现有的 QnA 对添加为后续提示符

原始问题`My account`有后续提示，如`Accounts and signing in`。 

![将正确返回原始的我的帐户问题帐户并登录回答，如果已经具有链接的后续提示。](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

添加到当前未链接的现有 QnA 对后续提示。 因为问题不链接到任何 QnA 对，需要更改当前的视图设置。

1. 若要链接现有 QnA 对作为后续提示符下，选择的问题和答案对的行。 图面上手动搜索`Sign out`可以减小列表。
1. 中的行`Signout`，选择**添加后续提示符**从**答案**列。
1. 在中**跟进提示符 （预览版）** 弹出窗口中，输入以下命令：

    |字段|值|
    |--|--|
    |显示文本|`Turn off the device`。 这是你选择要在后续提示符中显示的自定义文本。|
    |仅限上下文|选择。 如果问题指定上下文，将仅返回此答案。|
    |链接到答案|输入`Use the sign-in screen`查找现有的 QnA 对。|


1.  返回一个匹配项。 选择此答案作为执行后续操作，然后选择**保存**。 

    ![搜索跟进提示链接到现有答案，答案对话框使用答案的文本。](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 添加后续提示符后，务必选中**保存并训练**顶部导航栏中。
  
### <a name="edit-the-display-text"></a>编辑显示文本 

当创建后续提示符处，并为现有的 QnA 对选择作为**指向答案**，输入新**显示文本**。 此文本不会替换现有的问题，并不会添加一个新的备用问题。 它是独立于这些值。 

1. 若要编辑的显示文本，搜索并选择中的问题**上下文**字段。
1. 这个问题在行上，选择答案列中的后续提示符。 
1. 选择你想要编辑，然后选择的显示文本**编辑**。

    ![选择你想要编辑的显示文本，然后选择编辑。](../media/conversational-context/edit-existing-display-text.png)

1. **跟进提示符**弹出窗口，可更改现有的显示文本。 
1. 完成后编辑的显示文本，选择**保存**。 
1. 请记住选择**保存并训练**顶部导航栏中。


<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>添加新的 QnA 对后续提示

将新的 QnA 对添加到知识库。 QnA 对应作为后续提示链接到一个现有的问题。

1. 从知识库的工具栏中，搜索并选择的现有 QnA 对`Accounts and Signing In`。 

1. 在中**答案**对于此问题，选择的列**添加后续提示符**。 
1. **跟进提示符 （预览版）** ，通过输入以下值创建一个新的后续提示符： 

    |文本字段|值|
    |--|--|
    |**显示文本**|`Create a Windows Account`。 这是你选择要在后续提示符中显示的自定义文本。|
    |**Context-only**|选择。 如果问题指定上下文，将仅返回此答案。|
    |**若要回答的链接**|输入以下文本作为答案：<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>保存并训练数据库，此文本将转换为 |
    |||

    ![创建新提示 QnA](../media/conversational-context/create-child-prompt-from-parent.png)


1. 选择**新建**然后选择**保存**。 

    这创建新的问题和答案对并将链接作为后续提示所选的问题。 **上下文**列，这两个问题，指示后续提示关系。 

1. 更改**查看选项**到[显示上下文](#show-questions-and-answers-with-context)。

    新的问题显示了如何链接。

    ![创建新的后续提示 ](../media/conversational-context/new-qna-follow-up-prompt.png)

    父问题显示为其选择的一个新问题。

    ![![这两个问题的上下文列指示后续提示关系。](../ media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 添加后续提示符后，务必选中**保存并训练**顶部导航栏中。

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>启用多轮测试执行后续操作的提示时

在测试具有跟进问题的提示**测试**窗格中，选择**启用多轮次**，并输入您的问题。 响应包括后续提示。

![测试时，问题在测试窗格中，响应将包括后续提示。](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

如果未启用多轮次，将返回答案，而不是会返回后续提示。

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON 请求返回初始答案和后续提示

使用空`context`对象来请求用户的问题的答案，并包括后续提示。 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>要返回初始答案和后续提示的 JSON 响应

上一节所请求的答案和任何后续提示`Accounts and signing in`。 响应包括提示信息，位于`answers[0].context`，包含要向用户显示的文本。 

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
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
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

`prompts`阵列可提供中的文本`displayText`属性和`qnaId`值以便可以显示这些问题的答案为会话中的下一步显示选择流，然后发送到 QnA Maker 以下请求中的所选值。 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON 请求返回非初始答案和后续提示

填充`context`要包含以前的上下文对象。

在以下 JSON 请求中，当前的问题是`Use Windows Hello to sign in`和前一个问题是`Accounts and signing in`。 

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

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>要返回非初始答案和后续提示的 JSON 响应

QnA Maker _GenerateAnswer_ JSON 响应包括中的后续提示`context`属性中的第一项`answers`对象：

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

## <a name="query-the-knowledge-base-with-the-qna-id"></a>查询 id 为 QnA 知识库

在初始问题的响应、 任何后续的提示和其关联`qnaId`返回。 现在，你具有的 ID，您可以在后续提示符的请求正文中传递。 如果请求正文包含`qnaId`，和上下文对象 （其中包含以前的 QnA 属性），然后 GenerateAnswer 确切问题情况下将返回 ID，而不是使用排名算法以便查找答案的问题文本。 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>显示的提示，并在客户端应用程序中发送上下文 

已添加在知识库中的提示并在测试窗格中测试流。 现在，您需要在客户端应用程序中使用这些提示。 Bot Framework 的提示进行操作不会自动启动客户端应用程序中显示。 可以显示提示为建议的操作或按钮作为该用户的查询答案的一部分在客户端应用程序通过包含此[Bot Framework 示例](https://aka.ms/qnamakermultiturnsample)在代码中。 客户端应用程序应存储当前的 QnA ID 和用户查询，并将其传入[GenerateAnswer API 的上下文对象](#json-request-to-return-non-initial-answer-and-follow-up-prompts)下一步的用户查询。 

## <a name="display-order-supported-in-api"></a>在 API 中受支持的显示顺序

[显示的文本和显示顺序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)，JSON 响应中返回，以进行编辑的支持[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)。 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="next-steps"></a>后续步骤

了解有关上下文的对话的详细信息[对话框示例](https://aka.ms/qnamakermultiturnsample)或了解详细信息[概念智能机器人应用程序设计的多轮次会话](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)。

> [!div class="nextstepaction"]
> [迁移知识库](../Tutorials/migrate-knowledge-base.md)
