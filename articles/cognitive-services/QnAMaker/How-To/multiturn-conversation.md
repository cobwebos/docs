---
title: 多打开会话
titleSuffix: Azure Cognitive Services
description: 使用提示和上下文来管理多个将，称为多轮次，到另一个问题从智能机器人。 多轮次是能够进行后，包括反复对话，其中前一个问题的上下文会影响下一步的问题和答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508139"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>使用后续的提示操作以创建多个打开的会话

使用后续的提示和上下文来管理多个将，称为_多轮次_，到另一个问题从智能机器人。

若要查看多轮的工作原理，请查看下面的演示视频：

[![QnA Maker 中的多打开会话](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>什么是多轮次会话？

不能反过来单一回答一些问题。 在设计您的客户端应用程序 （聊天机器人） 对话时，用户可能会问一个问题就需要进行筛选和优化，以确定正确的答案。 使此流通过问题可以通过提供与用户*跟进提示*。

QnA Maker 时用户询问问题时，返回答案_和_任何后续提示。 此响应，可提供为选择的后续问题。 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>与聊天机器人的示例多打开会话

与多轮次的聊天机器人管理与用户来确定最终结果的会话，在下图中所示：

![包含提示，引导用户完成会话的多轮次对话框](../media/conversational-context/conversation-in-bot.png)

在上图中，用户已通过输入启动一个对话**我的帐户**。 知识库具有三个链接的问题和答案对。 若要改进答案，用户选择的三个选项之一中的知识。 在问题 (#1) 包含三个后续的提示，显示在聊天机器人为三个选项 (#2)。 

当用户选择一个选项 (#3) 时，显示下一个列表中的优化选项 (#4)。 此序列将继续 (#5)，直到用户确定正确的最终答案 (6)。

> [!NOTE]
> 在上图中，**启用多轮次**选中复选框以确保显示提示进行操作。 

### <a name="use-multi-turn-in-a-bot"></a>在智能机器人应用程序中使用多轮

若要管理上下文的会话，更改对客户端应用程序进行[将代码添加到智能机器人](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting)。 添加代码，用户可以显示的提示。  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>从文档的结构创建多打开会话

创建知识库时,**填充知识库**部分显示**启用多轮次提取从 Url、.pdf 或.docx 文件**复选框。 

![用于启用多轮次提取复选框](../media/conversational-context/enable-multi-turn.png)

当选择此选项可导入的文档时，可以从文档结构隐式多打开会话。 如果存在该结构，QnA Maker 后续提示符下，对问题和解答为你创建导入过程的一部分。 

仅从 Url，PDF 文件，可以推断出多轮次结构或 DOCX 文件。 有关结构的示例，查看的映像[Microsoft Surface 用户手动 PDF 文件](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)。 由于此 PDF 文件的大小，QnA Maker 资源需要**搜索定价层**的**B** （15 个索引） 或更高版本。 

![![用户手册中的结构示例](../ media/conversational-context/import-file-with-conversational-structure.png）](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

当您导入 PDF 文档时，QnA Maker 确定执行后续操作中的提示结构创建对话式流。 

1. QnA Maker 中选择**创建的知识库**。
1. 创建或使用现有的 QnA Maker 服务。 在 Microsoft Surface 前面示例中，由于 PDF 文件太大，更小级别，使用 QnA Maker 服务，具有**搜索服务**的**B** （15 个索引） 或更高版本。
1. 输入在知识库的名称，例如**图面上手动**。
1. 选择**启用多轮次提取从 Url、.pdf 或.docx 文件**复选框。 
1. 选择图面上的手动 URL **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** 。

1. 选择**创建知识库**按钮。 

    创建知识库后，将显示的提示问题答案对视图。

## <a name="show-questions-and-answers-with-context"></a>显示问题和答案与上下文

只有那些具有上下文的对话减少显示的提示问题答案对。 

选择**查看选项**，然后选择**显示上下文 （预览版）** 。 该列表显示提示问题答案对，其中包含后续提示。 

![筛选上下文的对话提示问题答案对](../media/conversational-context/filter-question-and-answers-by-context.png)

第一列中显示多轮次上下文。

![!["上下文 （预览）"列](../ media/conversational-context/surface-manual-pdf-follow-up-prompt.png）](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

在上图中， **#1**指示粗体文本中的列，表示当前的问题。 父问题是一行中的顶级项。 其下方的任何问题是链接的问题和答案对。 这些项都是可选的以便您可以立即转到其他上下文项。 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>将现有的问题和答案对添加为后续提示符

原始问题**我的帐户**，包含后续提示进行操作，例如**帐户并登录**。 

!["帐户并登录"答案和后续提示](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

添加到当前未链接的现有问题答案对后续提示。 因为问题不链接到任何问题和答案对，需要更改当前的视图设置。

1. 若要链接现有的问题和答案对作为后续提示，请选择问题答案对所对应的行。 图面上手动搜索**注销**可以减小列表。
1. 中的行**Signout**，在**答案**列中，选择**添加后续提示符**。
1. 在中的字段**跟进提示符 （预览版）** 弹出窗口中，输入以下值：

    |字段|值|
    |--|--|
    |显示文本|输入**关闭该设备**。 这是要在后续提示符中显示的自定义文本。|
    |仅限上下文| 选中此复选框。 仅当问题指定上下文返回答案。|
    |若要回答的链接|输入**使用登录屏幕**查找现有的问题和答案对。|


1.  返回一个匹配项。 作为后续操作中，选择此答案，然后选择**保存**。 

    !["跟进提示符 （预览版）"页](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 添加后续提示符后，选择**保存并训练**顶部导航栏中。
  
### <a name="edit-the-display-text"></a>编辑显示文本 

当创建后续提示符处，并为现有的问题和答案对输入为**指向答案**，输入新**显示文本**。 此文本不会替换现有的问题，并且它不会添加一个新的备用问题。 它是独立于这些值。 

1. 若要编辑的显示文本，搜索并选择中的问题**上下文**字段。
1. 在这个问题的行中，选择答案列中的后续提示。 
1. 选择你想要编辑，然后选择的显示文本**编辑**。

    ![编辑命令的显示文本](../media/conversational-context/edit-existing-display-text.png)

1. 在中**跟进提示符**弹出窗口中，更改现有的显示文本。 
1. 完成后编辑的显示文本，选择**保存**。 
1. 在顶部导航栏中，**保存并训练**。


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>添加新的问题和答案对作为后续提示

时将新的问题和答案对添加到知识库，则每个对应链接到一个现有的问题中作为后续提示。

1. 在知识库工具栏中，搜索并选择的现有问题答案对**帐户并登录**。 

1. 在中**答案**对于此问题，选择的列**添加后续提示符**。 
1. 下**跟进提示符 （预览版）** ，通过输入以下值创建一个新的后续提示符： 

    |字段|值|
    |--|--|
    |显示文本|*创建 Windows 帐户*。 要在后续提示符中显示的自定义文本。|
    |仅限上下文|选中此复选框。 仅当问题指定上下文，则返回此答案。|
    |若要回答的链接|输入以下文本作为答案：<br>*[创建](https://account.microsoft.com/)具有新的或现有的电子邮件帐户的 Windows 帐户*。<br>保存并训练数据库，此文本将被转换。 |
    |||

    ![创建新的提示问题和答案](../media/conversational-context/create-child-prompt-from-parent.png)


1. 选择**新建**，然后选择**保存**。 

    此操作将创建一个新的问题和答案对和所选问题的链接作为后续提示。 **上下文**列，这两个问题，指示后续提示关系。 

1. 选择**查看选项**，然后选择[**显示上下文 （预览版）** ](#show-questions-and-answers-with-context)。

    新的问题显示了如何链接。

    ![创建新的后续提示](../media/conversational-context/new-qna-follow-up-prompt.png)

    父问题显示为其选择的一个新问题。

    ![![这两个问题的上下文列指示后续提示关系](../ media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 添加后续提示符后，选择**保存并训练**顶部导航栏中。

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>启用多轮次的后续提示测试期间

在您测试具有跟进问题中将提示**测试**窗格中，选择**启用多轮次**，然后输入您的问题。 响应包括后续提示。

![响应包括后续提示](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

如果未启用多轮次，返回答案，而不是会返回后续提示。

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>返回初始答案和后续提示的 JSON 请求

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

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>JSON 响应，以返回初始答案和后续提示

在上一部分所请求的答案和任何后续提示**帐户并登录**。 响应包括提示信息，它位于*答案 [0].context*，并向用户显示的文本。 

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

`prompts`阵列可提供中的文本`displayText`属性和`qnaId`值。 可以显示这些问题的答案，如会话中的下一步显示的选择流，然后发送所选`qnaId`回 QnA Maker 在下面的请求。 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>返回一个非初始的答案和后续提示的 JSON 请求

填充`context`要包含以前的上下文对象。

在以下 JSON 请求中，当前的问题是*使用 Windows Hello 登录*和前一个问题是*帐户并登录*。 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>JSON 响应，返回一个非初始的答案和后续提示

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>查询知识库，QnA Maker id

在初始问题的响应、 任何后续的提示和其关联`qnaId`返回。 现在，你具有的 ID，您可以在后续提示符的请求正文中传递。 如果请求正文包含`qnaId`，和上下文对象 （其中包含以前的 QnA Maker 属性），然后 GenerateAnswer 确切问题情况下将返回 ID，而不是使用排名算法以便查找答案的问题文本。 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>显示提示并在客户端应用程序中发送上下文 

已添加在知识库中的提示并在测试窗格中测试流。 现在，您需要在客户端应用程序中使用这些提示。 Bot Framework 的提示进行操作不会自动显示在客户端应用程序中。 您可以提示通过显示为建议的操作或按钮作为对客户端应用程序中的用户的查询的响应的一部分包括这[Bot Framework 示例](https://aka.ms/qnamakermultiturnsample)在代码中。 客户端应用程序应存储当前的 QnA Maker ID 和用户查询，并将其传入[GenerateAnswer API 的上下文对象](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts)下一步的用户查询。 

## <a name="display-order-is-supported-in-the-update-api"></a>更新 API 中支持显示顺序

[显示的文本和显示顺序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)，JSON 响应中返回，以进行编辑的支持[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)。 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>创建具有多轮次提示的知识库，用于创建 API

可以使用多轮提示创建知识用例[QnA Maker 创建 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。 这在添加提示`context`属性的`prompts`数组。 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>添加或删除多轮次出现提示时使用更新 API

可以添加或删除多轮次提示使用[QnA Maker 更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)。  这在添加提示`context`属性的`promptsToAdd`数组和`promptsToDelete`数组。 


## <a name="next-steps"></a>后续步骤

了解有关此上下文的对话的详细信息[对话框示例](https://aka.ms/qnamakermultiturnsample)或者详细了解如何[概念智能机器人应用程序设计的多轮次会话](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)。

> [!div class="nextstepaction"]
> [迁移知识库](../Tutorials/migrate-knowledge-base.md)
