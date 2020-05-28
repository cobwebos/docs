---
title: 编辑知识库 - QnA Maker
description: QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 66adf9e1fe227f11514276147de32e8691db7fab
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993569"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>编辑知识库中的 QnA 对

QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。

QnA 对是从数据源（如文件或 URL）添加的，或作为编辑源添加的。 编辑源指示已在 QnA 门户中手动添加 QnA 对。 所有 QnA 对均可编辑。

<a name="add-an-editorial-qna-set"></a>

## <a name="add-an-editorial-qna-pair"></a>添加编辑 QnA 对

1. 登录到[QnA 门户](https://www.qnamaker.ai/)，然后选择要将 QnA 对添加到其中的知识库。
1. 在知识库的 "**编辑**" 页上，选择 "**添加 QnA" 对**以添加新的 QnA 对。

    > [!div class="mx-imgBorder"]
    > ![添加 QnA 对](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. 在 new QnA 对行中，添加所需的问题和答案字段。 其他字段是可选的。 所有字段均可随时更改。

1. （可选）添加**[替代短语](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)**。 替代短语是任何形式的问题，都与原始问题明显不同，但应提供相同的答案。

    当您的知识库发布并打开[活动的学习](use-active-learning.md)时，QnA Maker 会收集要接受的替代句式选项。 选择这些选项是为了增加预测准确性。

1. 还可以添加**[元数据](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)**。 若要查看元数据，请在上下文菜单中选择 "**查看选项**"。 元数据提供了对客户端应用程序（如聊天机器人）提供的答案的筛选器。

1. （可选）添加**[跟进提示](multiturn-conversation.md)**。 跟进提示向用户提供向客户端应用程序提供的其他会话路径。

1. 选择 "**保存并训练**" 以查看包含新 QnA 对的预测。

## <a name="rich-text-editing-for-answer"></a>用于答案的富文本编辑

通过丰富的文本编辑答案文本，您可以从简单的工具栏 markdown 样式。

1. 选择答案的文本区域，将在 "QnA" 对行中显示富文本编辑器工具栏。

    > [!div class="mx-imgBorder"]
    > ![富文本编辑器的屏幕截图，其中包含 QnA 对行的问题和答案。](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    答案中已有的任何文本会正确显示，因为用户会从机器人中看到它。

1. 编辑文本。 从富文本编辑工具栏选择 "格式设置功能"，或使用切换功能切换到 markdown 语法。

    > [!div class="mx-imgBorder"]
    > ![使用富文本编辑器编写文本格式并将其设置为 markdown。](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |丰富文本编辑器功能|键盘快捷方式|
    |--|--|
    |在富文本编辑器和 markdown 之间切换。 `</>`|Ctrl+M|
    |Bold. **B**|CTR + LB|
    |** _I_斜体，用斜体显示**|CTRL+I|
    |无序列表||
    |有序列表||
    |段落样式||
    |Image-从公共 URL 添加可用图像。|Ctrl+G|
    |添加指向公开提供的 URL 的链接。|Ctrl+K|
    |图释-从选择的图释中添加。|Ctrl+E|
    |高级菜单-撤消|CTRL+Z|
    |高级菜单-重做|Ctrl+Y|

1. 使用富文本工具栏中的图像图标向答案中添加一个图像。 就地编辑器需要可公开访问的图像 URL 和图像的替换文字。


    > [!div class="mx-imgBorder"]
    > ![使用富文本编辑器添加一个可公开访问的图像及其替换文字。](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. 通过选择答案中的文本，然后选择工具栏中的链接图标，或通过选择工具栏中的链接图标，然后输入新文本和 URL 来添加 URL 链接。

    > [!div class="mx-imgBorder"]
    > ![使用富文本编辑器添加一个可公开访问的图像及其替换文字。](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>编辑 QnA 对

无论原始数据源如何，都可以编辑任何 QnA 对中的任何字段。 由于当前的**视图选项**设置（在上下文工具栏中），某些字段可能不可见。

## <a name="delete-a-qna-pair"></a>删除 QnA 对

要删除 QnA，请单击 QnA 行最右侧的**** 删除图标。 这是一项永久性操作。 无法撤消。 请考虑在删除对之前从“发布”**** 页面导出知识库。

![删除 QnA 对](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>查找 QnA 对 ID

如果需要查找 QnA 对 ID，可以在两个位置找到它：

* 将鼠标悬停在你感兴趣的 QnA 对行上的 "删除" 图标上。 悬停文本包含 QnA 对 ID。
* 导出知识库。 知识库中的每个 QnA 对都包含 QnA 对 ID。

## <a name="add-alternate-questions"></a>添加替代问题

向现有 QnA 对添加替代问题，以提高与用户查询匹配的可能性。

![添加替代问题](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>链接 QnA 对

链接 QnA 对随[后续提示](multiturn-conversation.md)一起提供。 这是 QnA 对之间的逻辑连接，在知识库级别进行管理。 你可以在 QnA Maker 门户中编辑跟进提示。

无法链接答案的元数据中的 QnA 对。

## <a name="add-metadata"></a>添加元数据

首先选择 "**查看选项**"，然后选择 "**显示元数据**"，添加元数据对。 这会显示元数据列。 接下来，选择 **+** 签名以添加元数据对。 此对包含一个键和一个值。

## <a name="save-changes-to-the-qna-pairs"></a>保存对 QnA 对的更改

进行编辑后定期选择**保存和训练**，以避免丢失更改。

![添加元数据](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>何时使用富文本编辑和 markdown

使用[文本](#add-an-editorial-qna-set)格式的答案，您可以像作者一样，使用格式设置工具栏快速选择并设置文本格式。

当你需要自动生成内容来创建要作为 CI/CD 管道的一部分导入的知识库或用于[批处理测试](../Quickstarts/batch-testing.md)时， [Markdown](../reference-markdown-format.md)是一个更好的工具。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [针对知识库进行协作](./collaborate-knowledge-base.md)

* [管理 QnA Maker 使用的 Azure 资源](set-up-qnamaker-service-azure.md)
