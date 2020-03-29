---
title: 设计知识库 - QnA 制造商
description: QnA Maker 知识库由一组问答 （QnA） 集和与每个 QnA 对关联的可选元数据组成。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76844308"
---
# <a name="question-and-answer-set-concepts"></a>问答集概念

知识库由问答 （QnA） 集组成。  每个集都有一个答案，一个集包含与该_答案_关联的所有信息。 答案可以大致类似于数据库行或数据结构实例。

## <a name="question-and-answer-sets"></a>问答集

问答 （QnA） 集中**所需的**设置包括：

* **一个问题**- 用户查询文本，用于 QnA Maker 的机器学习，以不同的措辞但相同的答案与用户问题文本保持一致
* **答案**- 集的答案是在用户查询与关联的问题匹配时返回的响应

每组由**ID**表示。

集的**可选**设置包括：

* **问题的替代方法 -** 这有助于 QnA Maker 返回对更多问题短语的正确答案
* **元数据**：元数据是与 QnA 对关联的标记，表示为键值对。 元数据标记用于筛选 QnA 对并限制对其执行查询匹配的集。
* **多转提示**，用于继续多转对话

![QnA Maker 知识库](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>以编辑身份添加到知识库

如果没有预先存在的内容来填充知识库，则可以在 QnA Maker 门户中以编辑方式添加 QnA 集。 在[此处](../How-To/edit-knowledge-base.md)了解如何更新知识库。

## <a name="editing-your-knowledge-base-locally"></a>在本地编辑知识库

创建知识库后，建议在 [QnA Maker 门户](https://qnamaker.ai)中对知识库文本进行编辑，而不是通过导出并重新导入本地文件来进行编辑。 但是，有时可能需要在本地编辑知识库。

从“设置”**** 页面上导出知识库，然后使用 Microsoft Excel 编辑知识库。 如果选择使用其他应用程序编辑导出的文件，则应用程序可能会引入语法错误，因为它并不完全符合 TSV。 Microsoft Excel 的 TSV 文件通常不会引发任何格式设置错误。

在完成编辑后，从“设置”**** 页重新导入 TSV 文件。 这将完全将当前知识库替换为导入的知识库。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker 中的知识库生命周期](./development-lifecycle-knowledge-base.md)