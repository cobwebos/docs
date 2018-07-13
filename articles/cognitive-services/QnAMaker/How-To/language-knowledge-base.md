---
title: 如何创建非英语知识库 - QnA Maker - Azure 认知服务 | Microsoft Docs
description: 如何创建非英语知识库。
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 3fbd590229044af0daa60968fd8d556d539a58c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366284"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA Maker 的知识库内容的语言支持
QnA Maker 支持多种语言版本的知识库内容。 但是，每个 QnA Maker 服务应保留一种语言。 面向特定的 QnA Maker 服务创建的第一个知识库会设置该服务的语言。 有关支持的语言的完整列表，请参阅[此处](../Overview/languages-supported.md)。

会从正在提取的数据源的内容中自动识别语言。 创建新的 QnA Maker 服务和该服务中的新知识库后，可以验证是否已正确设置该语言。

1. 导航到 [Azure 门户](https://portal.azure.com/)。

2. 选择“资源组”并导航到已部署 QnA Maker 服务的资源组，然后选择“Azure 搜索”资源。

    ![选择“Azure 搜索”资源](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. 选择 testkb 索引。 此 Azure 搜索索引始终是第一个创建的索引，其中包含该服务中所有知识库的已保存内容。 

    ![选择测试知识库](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. 选择显示 testkb 详细信息的“字段”部分。

    ![选择字段](../media/qnamaker-how-to-language-kb/selectfields.png)

5. 选中“分析器”对应的框以查看语言详细信息。

    ![选择分析器](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. 你应该会发现分析器设置为特定语言。 知识库创建步骤期间自动检测到此语言。 一旦创建资源，就不能更改此语言。

    ![选定分析器](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure 机器人服务创建 QnA 机器人](../Tutorials/create-qna-bot.md)
