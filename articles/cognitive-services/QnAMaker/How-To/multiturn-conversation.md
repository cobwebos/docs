---
title: 多转对话 - QnA 制造商
description: 使用提示和上下文管理自动程序从一个问题到另一个问题的多个转弯（称为多转）。 多转折是进行反复对话的能力，其中前一个问题的上下文会影响下一个问题和答案。
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 8ef244e1b6baf480189bb90ea5ff53138a6f377a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261459"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>使用跟进提示创建多个轮次的对话

使用后续提示和上下文管理自动程序从一个问题到另一个问题的多个_转弯（称为多转_）。

要查看多转的工作原理，请查看以下演示视频：

[![QnA 制造商中的多转对话](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>什么是多转式对话？

有些问题不能一转就回答。 在设计客户端应用程序（聊天机器人）对话时，用户可能会提出需要筛选或优化以确定正确答案的问题。 通过向用户提供*后续提示*，可以通过这些问题进行此流。

当用户提问时，QnA Maker 会返回答案_和_任何后续提示。 此响应允许您将后续问题作为选择提出。

> [!CAUTION]
> 不会从常见问题解答文档中提取多转提示。 如果需要多转提取，请删除将 QnA 对指定为常见问题的问号。

## <a name="example-multi-turn-conversation-with-chat-bot"></a>使用聊天机器人进行多转对话的示例

通过多转，聊天机器人管理与用户的对话以确定最终答案，如下图所示：

![多转对话框，其中包含引导用户完成对话的提示](../media/conversational-context/conversation-in-bot.png)

在前面的映像中，用户通过输入**我的帐户**启动了对话。 知识库有三个链接的问答对。 要优化答案，用户选择知识库中的三个选项之一。 问题（#1）有三个后续提示，在聊天机器人中作为三个选项（#2）显示。

当用户选择选项（#3）时，将显示下一个优化选项列表（#4）。 此序列将继续（#5），直到用户确定正确的最终答案（#6）。


### <a name="use-multi-turn-in-a-bot"></a>在自动程序中使用多转

发布知识库后，您可以选择 **"创建自动程序"** 按钮，将 QnA Maker 机器人部署到 Azure 机器人服务。 提示将显示在您为机器人启用的聊天客户端中。

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>从文档结构创建多圈对话

创建知识库时，"**填充知识库"** 部分将显示**从 URL、.pdf 或 .docx 文件启用多转提取**复选框。

![启用多转提取的复选框](../media/conversational-context/enable-multi-turn.png)

选择此选项时，QnA Maker 会提取文档结构中存在的层次结构。 层次结构将转换为后续提示，层次结构的根用作父 QnA。 在某些文档中，层次结构的根目录没有可以用作答案的内容，您可以提供"默认答案文本"，作为替代答案文本来提取此类层次结构。

只能从 URL、PDF 文件或 DOCX 文件中推断多转结构。 有关结构的示例，请查看[Microsoft Surface 用户手册 PDF 文件](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)的图像。

![![用户手册中的结构示例](../媒体/对话上下文/导入文件-与对话结构.png）](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>构建您自己的多转文档

如果要创建多转文档，请记住以下准则：

* 使用标题和子标题表示层次结构。 例如，您可以 h1 表示父 QnA 和 h2 以表示应作为提示符的 QnA。 使用小标题大小表示后续层次结构。 不要使用样式、颜色或其他机制来暗示文档中的结构，QnA Maker 不会提取多转提示。

* 标题的第一个字符必须大写。

* 不要用问号结束标题。 `?`

* 可以使用[示例文档](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)创建自己的多转文档。

### <a name="adding-files-to-a-multi-turn-kb"></a>将文件添加到多转 KB

添加分层文档时，QnA Maker 会确定结构中的后续提示以创建对话流。

1. 在 QnA Maker 中，选择使用**启用 URL、.pdf 或 .docx 文件进行多转提取**创建的现有知识库。 启用。
1. 转到 **"设置"** 页，选择要添加的文件或 URL。
1. **保存并培训**知识库。

> [!Caution]
> 不支持支持将导出的 TSV 或 XLS 多转知识库文件用作新知识库的数据源。 您需要从 QnA Maker 门户的 **"设置"** 页**导入**该文件类型，以便将导出的多转提示添加到知识库。


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>使用创建 API 创建具有多转提示的知识库

您可以使用[QnA 制造商创建 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)创建具有多转提示的知识案例。 提示正在`context`属性的`prompts`数组中添加。

## <a name="show-questions-and-answers-with-context"></a>使用上下文显示问题和答案

将显示的问答对减少到仅具有上下文对话的对。

选择 **"查看"选项**，然后选择 **"显示上下文**"。 该列表显示包含后续提示的问答对。

![按上下文对话筛选问答对](../media/conversational-context/filter-question-and-answers-by-context.png)

多转上下文显示在第一列中。

![![上下文（预览）"列](../媒体/对话上下文/表面-手动-pdf-后续提示.png）](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

在前面的图像中 **，#1**表示列中的粗体文本，表示当前问题。 父问题是行中的顶部项。 它下面的任何问题都是链接的问答对。 这些项目是可选的，因此您可以立即转到其他上下文项。

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>将现有问答对添加为后续提示

原始问题"**我的帐户**"具有后续提示，如 **"帐户"和"登录**"。

!["帐户和登录"答案和后续提示](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

向当前未链接的现有问答对添加后续提示。 由于问题未链接到任何问答对，因此需要更改当前视图设置。

1. 要将现有问答对链接为后续提示，请选择问答对的行。 对于 Surface 手册，搜索 **"注销"** 以减少列表。
1. 在"**应答**"列中"**注销"** 行中，选择 **"添加后续提示**"。
1. 在 **"后续"提示**弹出窗口中的字段中，输入以下值：

    |字段|值|
    |--|--|
    |显示文本|输入**关闭设备**。 这是要在后续提示中显示的自定义文本。|
    |仅上下文| 选中此复选框。 仅当问题指定上下文时，才会返回答案。|
    |链接回答|输入**使用登录屏幕**查找现有的问答对。|


1.  返回一个匹配项。 选择此答案作为后续答案，然后选择 **"保存**"。

    !["后续提示 （预览）"页面](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 添加后续提示后，在顶部导航中选择 **"保存"并训练**。

### <a name="edit-the-display-text"></a>编辑显示文本

创建后续提示，并将现有问答对输入为 **"答案链接**"时，可以输入新的 **"显示"文本**。 此文本不会替换现有问题，也不会添加新的备用问题。 它与这些值是分开的。

1. 要编辑显示文本，请在 **"上下文"** 字段中搜索并选择问题。
1. 在该行中，在答案列中选择后续提示。
1. 选择要编辑的显示文本，然后选择 **"编辑**"。

    ![显示文本的"编辑"命令](../media/conversational-context/edit-existing-display-text.png)

1. 在 **"后续"提示**弹出窗口中，更改现有显示文本。
1. 编辑完显示文本后，选择"**保存**"。
1. 在顶部导航栏中，**保存和训练**。


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>添加新的问答对作为后续提示

将新的问答对添加到知识库时，每对应链接到现有问题作为后续提示。

1. 在知识库工具栏上，搜索并选择"帐户"的现有问答对**并登录**。

1. 在"**回答**"列中，选择 **"添加后续提示**"。
1. 在**后续提示 （PREVIEW）** 下，通过输入以下值创建新的后续提示：

    |字段|值|
    |--|--|
    |显示文本|*创建 Windows 帐户*。 要在后续提示中显示的自定义文本。|
    |仅上下文|选中此复选框。 仅当问题指定上下文时，才会返回此答案。|
    |链接回答|输入以下文本作为答案：<br>*[使用](https://account.microsoft.com/)新的或现有的电子邮件帐户创建 Windows 帐户*。<br>保存并训练数据库时，将转换此文本。 |
    |||

    ![创建新的提示问题和答案](../media/conversational-context/create-child-prompt-from-parent.png)


1. 选择 **"创建新**"，然后选择 **"保存**"。

    此操作将创建新的问答对，并将所选问题作为后续提示链接。 对于这两个问题，"**上下文**"列指示后续提示关系。

1. 选择 **"查看"选项**，然后选择[**"显示上下文"（预览）。**](#show-questions-and-answers-with-context)

    新的问题显示了它是如何链接的。

    ![创建新的后续提示](../media/conversational-context/new-qna-follow-up-prompt.png)

    父问题显示一个新问题作为其选择之一。

    ![![对于这两个问题，上下文列指示后续提示关系](../媒体/对话上下文/儿童提示创建.png）](../media/conversational-context/child-prompt-created.png#lightbox)

1. 添加后续提示后，在顶部导航栏中选择 **"保存"和"训练**"。

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>在后续提示测试期间查看多转

在 **"测试"** 窗格中使用后续提示测试问题时，响应包括后续提示。

![响应包括后续提示](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>JSON 请求返回初始答案和后续提示

使用空`context`对象请求用户问题的答案，并包含后续提示。

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>JSON 响应，用于返回初始答案和后续提示

上一节要求答复和任何后续提示到**帐户和登录**。 响应包括位于*答案{0}上下文*的提示信息，以及要向用户显示的文本。

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

数组`prompts`提供`displayText`属性和值中`qnaId`的文本。 您可以将这些答案显示为对话流中的下一个显示选项，然后在以下请求中将所选`qnaId`选项发送回 QnA Maker。

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>JSON 请求返回非初始答案和后续提示

填充`context`对象以包括以前的上下文。

在下面的 JSON 请求中，当前的问题是*使用 Windows Hello 登录*，而上一个问题是*帐户和登录*。

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>JSON 响应，用于返回非初始答案和后续提示

QnA Maker _GenerateAnswer_ JSON 响应包括`context``answers`对象中第一项属性中的后续提示：

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
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>使用 QnA 制造商 ID 查询知识库

如果要使用多转功能构建自定义应用程序。 在初始问题的回答中，将返回任何后续提示及其关联`qnaId`。 现在您拥有了 ID，您可以在后续提示的请求正文中传递该 ID。 如果请求正文包含 和`qnaId`上下文对象（包含以前的 QnA Maker 属性），则"生成应答"将按 ID 返回确切的问题，而不是使用排名算法通过问题文本查找答案。


## <a name="display-order-is-supported-in-the-update-api"></a>更新 API 中支持显示顺序

在 JSON 响应中返回的[显示文本和显示顺序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)受[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)支持进行编辑。

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>使用更新 API 添加或删除多转提示

您可以使用[QnA 制造商更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)添加或删除多转提示。  提示正在`context`属性的`promptsToAdd`数组和`promptsToDelete`数组中添加。

## <a name="export-knowledge-base-for-version-control"></a>版本控制的导出知识库

QnA Maker 通过在导出的文件中包括多转对话步骤支持版本控制。

## <a name="next-steps"></a>后续步骤

从此[对话框示例](https://aka.ms/qnamakermultiturnsample)了解有关上下文对话的更多内容，或了解有关[多转对话的概念机器人设计](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)详细信息。

> [!div class="nextstepaction"]
> [迁移知识库](../Tutorials/migrate-knowledge-base.md)
