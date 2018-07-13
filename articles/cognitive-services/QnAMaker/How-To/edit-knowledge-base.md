---
title: 如何编辑知识库 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 如何编辑知识库
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: eaa65bf3d257399fceadaa42f0d9ddbbf8afe234
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366298"
---
# <a name="edit-a-knowledge-base"></a>编辑知识库

QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。

## <a name="edit-your-knowledge-base-content"></a>编辑知识库内容

1.  在顶部导航栏中选择“我的知识库”。 

    你可以按“上次修改时间”的降序排序，查看你创建或共享的所有服务。

    ![我的知识库](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. 选择特定的知识库以对其进行编辑。

3. 完成对知识库的更改后，单击页面右上角的“保存和训练”以保留更改。    

    ![保存和训练](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    在单击“保存和训练”之前离开页面将不会保留更改。

## <a name="add-a-qna-pair"></a>添加 QnA 对

选择“添加 QnA 对”以向知识库表添加新行。

![添加 QnA 对](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>删除 QnA 对

要删除 QnA，请单击 QnA 行最右侧的删除图标。

![删除 QnA 对](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>添加替代问题

向现有 QnA 对添加替代问题，以提高与用户查询匹配的可能性。

![添加替代问题](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>添加元数据


通过选择过滤器图标添加元数据对

![添加元数据](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> 确保在编辑后定期保存并定型知识库，以避免丢失更改。

## <a name="manage-large-knowledge-bases"></a>管理大型知识库

1. QnAs 按从中提取它们的数据源分组。 你可以展开或折叠数据源。
2. 你可以通过在知识库表顶部的文本框中键入内容来搜索知识库。 单击 Enter 以搜索问题、答案或元数据内容。 单击 X 图标以删除搜索筛选器。
3. “分页”允许你管理大型知识库

    ![搜索, 分页, 分组](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [针对知识库进行协作](./collaborate-knowledge-base.md)
