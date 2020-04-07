---
title: 编辑知识库 - QnA Maker
description: QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756716"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>在知识库中编辑 QnA 对

QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。

QnA 对从数据源（如文件或 URL）添加，或添加为编辑源。 编辑源指示 QnA 对已手动添加到 QnA 门户中。 所有 QnA 对都可用于编辑。

## <a name="add-an-editorial-qna-pair"></a>添加编辑 QnA 对
1. 登录到[QnA 门户](https://www.qnamaker.ai/)，然后选择知识库以将 QnA 对添加到 。
1. 在知识库的**EDIT**页上，选择 **"添加 QnA 对**"以添加新的 QnA 对。

1. 在新的 QnA 对行中，添加所需的**问答**字段 **。** 其他字段是可选的。 可以随时更改所有字段。

1. 可以选择添加**备用短语**。 替代措辞是与原始问题有显著差异但应提供相同答案的任何类型的问题。

    当您的知识库发布，并且您打开了[主动学习](use-active-learning.md)时，QnA Maker 会收集备用短语选项，供您接受。 选择这些选项是为了提高预测精度。

1. 可以选择添加**元数据**。 要查看元数据，请在上下文菜单中选择 **"查看"选项**。 元数据提供客户端应用程序（如聊天机器人）提供答案的筛选器。

1. 或者，添加**后续提示**。 后续提示为客户端应用程序提供了其他对话路径，以便向用户演示。

1. 选择 **"保存"并训练**以查看预测，包括新的 QnA 对。

## <a name="edit-a-qna-pair"></a>编辑 QnA 对

可以编辑任何 QnA 对中的任何字段，而不考虑原始数据源。 由于当前 **"视图选项**"设置（位于上下文工具栏中），某些字段可能不可见。

## <a name="delete-a-qna-pair"></a>删除 QnA 对

要删除 QnA，请单击 QnA 行最右侧的**** 删除图标。 这是一项永久性操作。 无法撤消。 请考虑在删除集之前从 **"发布"** 页导出知识库。

![删除 QnA 对](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>查找 QnA 对 ID

如果需要查找 QnA 对 ID，可以在以下两个位置找到它：

* 将鼠标悬停在您感兴趣的 QnA 对行上的删除图标上。 悬停文本包括 QnA 对 ID。
* 导出知识库。 知识库中的每个 QnA 对都包括 QnA 对 ID。

## <a name="add-alternate-questions"></a>添加替代问题

向现有 QnA 对添加替代问题，以提高与用户查询匹配的可能性。

![添加替代问题](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>链接 QnA 对

链接QnA对提供[后续提示](multiturn-conversation.md)。 这是在知识库级别管理的 QnA 对之间的逻辑连接。 您可以在 QnA Maker 门户中编辑后续提示。

无法在答案的元数据中链接 QnA 对。

## <a name="add-metadata"></a>添加元数据

通过首先选择 **"查看"选项**，然后选择 **"显示元数据**"来添加元数据集。 这将显示元数据列。 接下来，选择添加**+** 元数据集的符号。 此集由一个键和一个值组成。

## <a name="save-changes-to-the-qna-pairs"></a>保存对 QnA 对的更改

定期选择 **"保存"并在**进行编辑后进行训练，以避免丢失更改。

![添加元数据](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [针对知识库进行协作](./collaborate-knowledge-base.md)

* [管理 QnA 制造商使用的 Azure 资源](set-up-qnamaker-service-azure.md)