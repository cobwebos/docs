---
title: 编辑知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/06/2018
ms.author: tulasim
ms.openlocfilehash: adcefe8fed927aca2533ea811bac56f0b92288de
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51279692"
---
# <a name="edit-a-knowledge-base"></a>编辑知识库

QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。

## <a name="edit-your-knowledge-base-content"></a>编辑知识库内容

1.  在顶部导航栏中选择“我的知识库”。 

    你可以按“上次修改时间”的降序排序，查看你创建或共享的所有服务。

    ![我的知识库](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. 选择特定的知识库以对其进行编辑。
 
3. 单击“设置” 。

   可以在此处编辑必填字段“服务名称”。
  
   可以通过单击“管理知识库”- >“+ 添加 URL”链接，添加新的 URL，以便向知识库添加新的常见问题解答内容。
   
   可以通过单击“删除”图标来删除现有 URL。
   
   如果希望知识库抓取现有 URL 的最新内容，请勾选复选框名称“刷新”，这将使用最新 URL 内容更新知识库。
   
可以通过单击“管理知识库”- >“+ 添加文件”将支持的文件文档添加到知识库中

还可以通过单击“导入知识库”按钮导入任何现有知识库。 
   
知识库的更新取决于在创建与知识库关联的 QnA Maker 服务时使用的**管理定价层**。 如果需要，还可以从 Azure 门户更新管理层。

4. 完成对知识库的更改后，单击页面右上角的“保存和训练”以保留更改。    

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

## <a name="delete-knowledge-bases"></a>删除知识库

删除知识库 (KB) 是一项永久性操作， 无法撤消。 删除知识库之前，应该从 QnA Maker 门户的“设置”页导出知识库。 

如果你与[协作者](collaborate-knowledge-base.md)共享知识库，然后将该知识库删除，则每个人都会失去对该知识库的访问权限。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [针对知识库进行协作](./collaborate-knowledge-base.md)
