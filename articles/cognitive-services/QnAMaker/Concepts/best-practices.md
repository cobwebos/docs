---
title: 最佳做法 - QnA Maker - Azure 认知服务 | Microsoft Docs
description: 使用这些最佳做法来改进知识库，并向应用程序/聊天机器人的最终用户提供更好的结果。
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "35366909"
---
# <a name="best-practices"></a>最佳实践
[知识库开发生命周期](../Concepts/development-lifecycle-knowledge-base.md)将指导如何端到端地管理知识库。 使用这些最佳做法来改进知识库，并向应用程序/聊天机器人的最终用户提供更好的结果。

## <a name="extraction"></a>提取
QnA Maker 持续改进着从内容提取 QnA 的算法，并扩展支持的文件和 HTML 页面格式的列表。 请根据要从中提取内容的文档类型，遵循提取的[指导](../Concepts/data-sources-supported.md)。 

一般情况下，常见问题解答页面应单独存在，且不会与其他信息合并。 产品手册应该具备明确的标题，并且最好有一个索引页。 

## <a name="rankingmatching"></a>排名/匹配
请确保自己充分利用了 QnA Maker 支持的排名功能。 这样可提高给定的用户查询获得适当响应的可能性。

### <a name="add-alternate-questions"></a>添加替代问题
[替代问题](../How-To/edit-knowledge-base.md)可提高用户查询匹配到结果的可能性。 如果同样的问题能以多种方式提出，则替代问题就很有用。 这可以包含句子结构中的更改（例如“有地方停车吗？” 和“是否有停车位？”）或用词风格以及俚语的更改（例如“嗨”、“哟”以及“嘿”）。

### <a name="use-metadata-filters"></a>使用元数据筛选器
[元数据](../How-To/edit-knowledge-base.md)可增加根据筛选器缩小用户查询结果的范围的能力。 即使查询相同，知识库的答案也可能因元数据标记而有所不同。 例如，如果餐馆分店的位置不同（即，元数据是“位置：西雅图”和“位置：雷德蒙德”），则“停车位在哪里？”就会有不同的答案”。

### <a name="use-synonyms"></a>使用同义词
英语中对同义词提供一定程度的支持，使用 [word alterations](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) 将同义词添加到具有不同形式的关键字中（例如：buy -> purchase 或 netbanking -> net banking。 同义词应添加到 QnA Maker 服务级别并由服务中的所有知识库共享。

### <a name="use-distinct-words-to-differentiate-questions"></a>使用不同的词来区分问题
如果每个问题都有不同的需求，那么最好是使用 QnA Maker 的匹配排名算法，这些算法将用户查询与知识库中的问题相匹配。 如果问题间存在重复的相同词组，则会降低为含有这些词的给定用户查询选择正确答案的概率。

## <a name="collaborate"></a>协作
QnA Maker 让用户可以在知识库上进行[协作](../How-to/collaborate-knowledge-base.md)。 用户需要具备对 Azure QnA Maker 资源组的访问权限，以便访问知识库。 某些组织可能想外包知识库的编辑工作和维护工作，但仍要能保护 Azure 资源的访问权限。 通过在不同订阅中设置两个完全相同的 [QnA maker 服务](../How-to/set-up-qnamaker-service-azure.md)并指定一个用于编辑测试循环，即可完成编辑者-审批者模型。 完成测试后，可使用[导入-导出](../Tutorials/migrate-knowledge-base.md)进程将知识库内容转移到审批者 QnA Maker 服务，由审批者进行最终的知识库发布和终结点更新。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编辑知识库](../How-to/edit-knowledge-base.md)

