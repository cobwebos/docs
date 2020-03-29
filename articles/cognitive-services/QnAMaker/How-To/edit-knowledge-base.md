---
title: 编辑知识库 - QnA Maker
description: QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131659"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>在知识库中编辑 QnA 集

QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。

QnA 集从数据源（如文件或 URL）添加，或添加为编辑源。 编辑源指示 QnA 集已手动添加到 QnA 门户中。 所有 QnA 集都可用于编辑。

## <a name="add-an-editorial-qna-set"></a>添加编辑 QnA 集
1. 登录到[QnA 门户](https://www.qnamaker.ai/)，然后选择知识库以将 QnA 集添加到 。
1. 在知识库的**EDIT**页上，选择 **"添加 QnA 集**"以添加新的 QnA 集。

1. 在新的 QnA 设置行中，添加所需的**问答**字段 **。** 其他字段是可选的。 可以随时更改所有字段。

1. 可以选择添加**备用短语**。 替代措辞是与原始问题有显著差异但应提供相同答案的任何类型的问题。

    当您的知识库发布，并且您打开了主动学习时，QnA Maker 会收集备用短语选项，供您接受。 选择这些选项是为了提高预测精度。

1. 可以选择添加**元数据**。 要查看元数据，请在上下文菜单中选择 **"查看"选项**。 元数据提供客户端应用程序（如聊天机器人）提供答案的筛选器。

1. 或者，添加**后续提示**。 后续提示为客户端应用程序提供了其他对话路径，以便向用户演示。

1. 选择 **"保存"并训练**以查看预测，包括新的 QnA 集。

## <a name="edit-a-qna-set"></a>编辑 QnA 集

可以编辑任何 QnA 集中的任何字段，而不考虑原始数据源。 由于当前 **"视图选项**"设置（位于上下文工具栏中），某些字段可能不可见。

## <a name="delete-a-qna-set"></a>删除 QnA 集

要删除 QnA，请单击 QnA 行最右侧的**** 删除图标。 这是一项永久性操作。 无法撤消。 请考虑在删除集之前从 **"发布"** 页导出知识库。

![删除 QnA 集](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>查找 QnA 设置 ID

如果需要查找 QnA 集 ID，可以在以下两个位置找到它：

* 将鼠标悬停在您感兴趣的 QnA 设置行上的删除图标上。 悬停文本包括 QnA 设置 ID。
* 导出知识库。 知识库中的每个 QnA 集都包括 QnA 集 ID。

## <a name="add-alternate-questions"></a>添加替代问题

将备用问题添加到现有 QnA 集，以提高与用户查询匹配的可能性。

![添加替代问题](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>链接 QnA 集

链接 QnA 集提供[后续提示](multiturn-conversation.md)。 这是在知识库级别管理的 QnA 集之间的逻辑连接。 您可以在 QnA Maker 门户中编辑后续提示。

无法在答案的元数据中链接 QnA 集。

## <a name="add-metadata"></a>添加元数据

通过首先选择 **"查看"选项**，然后选择 **"显示元数据**"来添加元数据集。 这将显示元数据列。 接下来，选择添加**+** 元数据集的符号。 此集由一个键和一个值组成。

## <a name="save-changes-to-the-qna-sets"></a>保存对 QnA 集的更改

定期选择 **"保存"并在**进行编辑后进行训练，以避免丢失更改。

![添加元数据](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [针对知识库进行协作](./collaborate-knowledge-base.md)

* [管理 QnA 制造商使用的 Azure 资源](set-up-qnamaker-service-azure.md)