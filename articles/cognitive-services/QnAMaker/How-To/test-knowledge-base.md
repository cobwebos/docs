---
title: 如何测试知识库 - QnA Maker - Azure 认知服务 | Microsoft Docs
description: 在发布知识库之前对其进行测试。
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: cffb63666edab25e1b3b0739d0e0f2f828600f3a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366299"
---
# <a name="test-your-knowledge-base"></a>测试知识库

测试 QnA Maker 知识库是迭代过程的重要部分，可以提高返回响应的准确性。 可以通过增强的聊天界面测试知识库，该界面还允许你进行编辑。

## <a name="test-answer-matching"></a>测试答案匹配

1.  通过在“我的知识库”页上选择知识库名称来访问知识库。
2.  若要访问“测试”滑出面板，请在应用程序的顶部面板中选择“测试”。

    ![访问测试](../media/qnamaker-how-to-test-kb/access-test.png)

3.  在文本框中输入查询，然后选择 Enter。

4.  知识库中最匹配的答案将作为响应返回。

## <a name="clear-test-panel"></a>清除测试面板

若要从测试控制台中清除所有输入的测试查询及其结果，请选择“测试”面板左上角的“重新开始”。

## <a name="close-test-panel"></a>关闭测试面板

若要关闭“测试”面板，请再次选择“测试”按钮。 当“测试”面板处于打开状态时，无法编辑知识库内容。

## <a name="inspect-score"></a>检查分数

在“检查”面板中检查测试结果的详细信息。

1.  打开“测试”滑出面板后，选择“检查”以获取有关该响应的更多详细信息。

    ![检查响应](../media/qnamaker-how-to-test-kb/inspect.png)

2.  此时将显示“检查”面板。 此面板包括评分最高的意向以及任何已识别的实体。 此面板显示所选陈述的结果。

## <a name="correct-the-top-scoring-answer"></a>更正评分最高的答案

如果评分最高的答案不正确，请从列表中选择正确答案，然后选择“保存并训练”。

![访问测试](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>添加替代问题

可以将问题的替代形式添加到给定答案中。 在文本框中键入备用答案，然后单击 Enter 以添加它们。 选择“保存并训练”以存储更新。

![访问测试](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>添加新答案

如果匹配的任何现有答案不正确或知识库中不存在答案（在知识库中找不到良好匹配），则可以添加新答案。 在文本框中输入当前问题的新答案，然后按 Enter 将其添加。 

选择“保存并训练”保存此答案。 现在，新的问答对已添加到知识库中。

![访问测试](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> 只有在按下“保存并训练”按钮时，才会保存对知识库的所有编辑。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [发布知识库](./publish-knowledge-base.md)
