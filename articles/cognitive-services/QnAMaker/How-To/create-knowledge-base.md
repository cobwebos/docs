---
title: 如何创建知识库 - QnA Maker - Azure 认知服务 | Microsoft Docs
description: 如何创建知识库
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "35366912"
---
# <a name="create-a-knowledge-base"></a>创建知识库

使用 QnA Maker 可以轻松地载入现有数据源来创建知识库。 可以从常见问题解答页面、产品手册或结构化文档新建 QnA Maker 知识库，或者通过编辑方式添加它们。

## <a name="steps"></a>步骤

1. 要开始操作，请使用 Azure 凭据登录至 [QnA Maker 门户](https://qnamaker.ai)并单击“新建服务”。

    ![创建知识库 ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. 如果尚未创建 QnA Maker 服务，请选择“创建 QnA Maker 服务”。 否则，从步骤 2 中的下拉列表选择一个 QnA Maker 服务。 选择要托管该知识库的 QnA Maker 服务。

    ![设置 QnA 服务](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. 若创建知识库，请输入以下信息。

    ![设置数据源](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 为服务提供一个“名称”。 支持重复的名称，也可以使用特殊字符。
    - 粘贴要从中提取内容的 URL。 若要详细了解支持的源类型，请查看[此处](../Concepts/data-sources-supported.md)。
    - 也可以上传从中提取数据的文件。 若要了解能添加的文档数量，请参阅[定价信息](https://aka.ms/qnamaker-pricing
)。
    - 若想手动添加 QnA，可以跳过链接文件。

4. 选择**创建**。

    ![创建知识库](../media/qnamaker-how-to-create-kb/create-kb.png)

5. 提取数据需要几分钟时间。

    ![提取](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. 如果成功创建了数据库，将重定向至“知识库”页面。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [导入知识库](../Tutorials/migrate-knowledge-base.md)
