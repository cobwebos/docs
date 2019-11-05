---
title: 如何测试知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 测试 QnA Maker 知识库是迭代过程的重要部分，可以提高返回响应的准确性。 可以通过增强的聊天界面测试知识库，该界面还允许你进行编辑。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 64b4b9a4ad5ceb0b3c33ae022b34daeafda93a62
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491201"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>在 QnA Maker 中测试您的知识库

测试 QnA Maker 知识库是迭代过程的重要部分，可以提高返回响应的准确性。 可以通过增强的聊天界面测试知识库，该界面还允许你进行编辑。

## <a name="interactively-test-in-qna-maker-portal"></a>在 QnA Maker 门户中以交互方式测试

1. 通过在“我的知识库”页上选择知识库名称来访问知识库。
1. 若要访问“测试”滑出面板，请在应用程序的顶部面板中选择“测试”。
1. 在文本框中输入查询，然后选择 Enter。
1. 知识库中最匹配的答案将作为响应返回。

### <a name="clear-test-panel"></a>清除测试面板

若要从测试控制台中清除所有输入的测试查询及其结果，请选择“测试”面板左上角的“重新开始”。

### <a name="close-test-panel"></a>关闭测试面板

若要关闭“测试”面板，请再次选择“测试”按钮。 当“测试”面板处于打开状态时，无法编辑知识库内容。

### <a name="inspect-score"></a>检查分数

在“检查”面板中检查测试结果的详细信息。

1.  打开“测试”滑出面板后，选择“检查”以获取有关该响应的更多详细信息。

    ![检查响应](../media/qnamaker-how-to-test-kb/inspect.png)

2.  此时将显示“检查”面板。 此面板包括评分最高的意向以及任何已识别的实体。 此面板显示所选陈述的结果。

### <a name="correct-the-top-scoring-answer"></a>更正评分最高的答案

如果评分最高的答案不正确，请从列表中选择正确答案，然后选择“保存并训练”。

![更正评分最高的答案](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>添加替代问题

可以将问题的替代形式添加到给定答案中。 在文本框中键入备用答案，然后单击 Enter 以添加它们。 选择“保存并训练”以存储更新。

![添加替代问题](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>添加新答案

如果匹配的任何现有答案不正确或知识库中不存在答案（在知识库中找不到良好匹配），则可以添加新答案。 

在 "答案" 列表的底部，使用文本框输入新答案并按 enter 添加新答案。 

选择“保存并训练”保存此答案。 现在，新的问答对已添加到知识库中。 

> [!NOTE]
> 只有在按下“保存并训练”按钮时，才会保存对知识库的所有编辑。

### <a name="test-the-published-knowledge-base"></a>测试已发布的知识库

您可以在 "测试" 窗格中测试知识库的已发布版本。 发布知识库后，选择 "**已发布的 kb** " 框，并发送一个查询以从发布的 kb 获取结果。

![针对发布的 KB 进行测试](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>带有工具的批处理测试

如果要执行以下操作，请使用批处理测试工具：

* 确定一组问题的顶级答案和分数
* 验证问题集的预期答案

批处理测试工具随批处理测试工具一起提供。 此工具作为可下载的[压缩可执行文件](https://qnamakerstore.blob.core.windows.net/qnamakerdata/batchtesting/bt.zip)或作为[ C#源代码](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)提供。 

[有关该工具的参考文档](../reference-tsv-format-batch-testing.md)包括：

* 此工具的命令行示例
* TSV 输入和 outfile 文件的格式 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [发布知识库](./publish-knowledge-base.md)
