---
title: 设计知识库-QnA Maker
description: QnA Maker 知识库包含一组问题和答案（QnA）对以及与每个 QnA 对关联的可选元数据。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e75a9313f02a6b5cc4235cdcccb381585d2192ae
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992991"
---
# <a name="question-and-answer-pair-concepts"></a>问题和答案对概念

知识库包含问题和答案（QnA）对。  每个对都有一个答案，一对包含与该_答案_关联的所有信息。 答案与数据库行或数据结构实例的可能松散类似。

## <a name="question-and-answer-pairs"></a>问题和答案对

问答（QnA）对中的**必需**设置如下：

* 用户查询的**问题**文本，用于 QnA Maker 机器学习，与用户的问题文本与不同的措辞保持一致，但回答相同
* **答案**-该对的答案是在用户查询与关联的问题匹配时返回的响应

每个对由**ID**表示。

对的**可选**设置包括：

* **问题的替代形式**-这有助于 QnA Maker 返回更多问题的正确答案句式
* **元**数据：元数据是与 QnA 对关联的标记，以键值对的形式表示。 元数据标记用于筛选 QnA 对并限制对其执行查询匹配的集。
* **多提示**，用于继续多轮对话

![QnA Maker 知识库](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>以编辑身份添加到知识库

如果没有预先存在的内容来填充知识库，可以在 QnA Maker 门户中添加 QnA 对 editorially。 在[此处](../How-To/edit-knowledge-base.md)了解如何更新知识库。

## <a name="editing-your-knowledge-base-locally"></a>在本地编辑知识库

创建知识库后，建议在 [QnA Maker 门户](https://qnamaker.ai)中对知识库文本进行编辑，而不是通过导出并重新导入本地文件来进行编辑。 但是，有时可能需要在本地编辑知识库。

从“设置”**** 页面上导出知识库，然后使用 Microsoft Excel 编辑知识库。 如果你选择使用其他应用程序来编辑导出的文件，则应用程序可能会引入语法错误，因为它不完全符合 TSV 标准。 Microsoft Excel 的 TSV 文件通常不会引发任何格式设置错误。

在完成编辑后，从“设置”**** 页重新导入 TSV 文件。 这将完全将当前知识库替换为导入的知识库。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker 中的知识库生命周期](./development-lifecycle-knowledge-base.md)