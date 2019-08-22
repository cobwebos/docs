---
title: 非英语知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 支持多种语言版本的知识库内容。 但是，每个 QnA Maker 服务应保留一种语言。 面向特定的 QnA Maker 服务创建的第一个知识库会设置该服务的语言。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 63eb13dd131fcc1c424c02fdac10f531cc9f0282
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876625"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA Maker 的知识库内容的语言支持

QnA Maker 支持多种语言版本的知识库内容。 但是，每个 QnA Maker 服务应保留一种语言。 面向特定的 QnA Maker 服务创建的第一个知识库会设置该服务的语言。 有关支持的语言的完整列表，请参阅[此处](../Overview/languages-supported.md)。

会从正在提取的数据源的内容中自动识别语言。 创建新的 QnA Maker 服务和该服务中的新知识库后，可以验证是否已正确设置该语言。

1. 导航到 [Azure 门户](https://portal.azure.com/)。

1. 选择“资源组”并导航到已部署 QnA Maker 服务的资源组，然后选择“Azure 搜索”资源。

    ![选择“Azure 搜索”资源](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. 选择 testkb 索引。 此 Azure 搜索索引始终是第一个创建的索引，其中包含该服务中所有知识库的已保存内容。 

    ![选择测试知识库](../media/qnamaker-how-to-language-kb/select-testkb.png)

1. "选择**字段**" 部分显示_testkb_详细信息。

    ![选择字段](../media/qnamaker-how-to-language-kb/selectfields.png)

1. 选中“分析器”对应的框以查看语言详细信息。

    ![选择分析器](../media/qnamaker-how-to-language-kb/select-analyzer.png)

1. 你应会发现_分析器_设置为特定语言。 在从导入的文件和 Url 创建知识库的过程中, 会自动检测到此语言。 一旦创建资源，就不能更改此语言。

    ![选定分析器](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure 机器人服务创建 QnA 机器人](../Tutorials/create-qna-bot.md)
