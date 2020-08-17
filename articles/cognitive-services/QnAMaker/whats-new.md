---
title: QnA Maker 服务有哪些新功能？
titleSuffix: Azure Cognitive Services
description: 本文包含有关 QnA Maker 的新闻。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 07/16/2020
ms.openlocfilehash: 73044f35b90fd3e5996899fd7c3b0a925056f8ed
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836864"
---
# <a name="whats-new-in-qna-maker"></a>QnA Maker 中的新增功能

了解服务中的新增功能。 这些项可能包括发布说明、视频、博客文章和其他类型的信息。 将此页加入书签，以随时了解服务的最新信息。

## <a name="release-notes"></a>发行说明

了解 QnA Maker 的新增功能。

### <a name="july-2020"></a>2020 年 7 月

* [元数据：多个元数据对的 `OR` 逻辑组合](how-to/metadata-generateanswer-usage.md#logical-or-using-strictfilterscompoundoperationtype-property)
* 将认知搜索终结点配置为私有，但仍可供 QnA Maker 访问的[步骤](how-to/set-up-qnamaker-service-azure.md#configuring-cognitive-search-as-a-private-endpoint-inside-a-vnet)。
* [90 天不活动](how-to/set-up-qnamaker-service-azure.md#inactivity-policy-for-free-search-resources)后，将删除免费认知搜索资源。

### <a name="june-2020"></a>2020 年 6 月

* 更新了 [Power Virtual Agent](tutorials/integrate-with-power-virtual-assistant-fallback-topic.md) 教程，使步骤执行起来更迅速、更简单

### <a name="may-2020"></a>2020 年 5 月

* [Azure 基于角色的访问控制 (Azure RBAC)](concepts/role-based-access-control.md)
* 用于回答的[富文本编辑](how-to/edit-knowledge-base.md#rich-text-editing-for-answer)

### <a name="march-2020"></a>2020 年 3 月

* 现在，将对此服务的所有 HTTP 请求强制执行 TLS 1.2。 有关详细信息，请参阅 [Azure 认知服务安全性](../cognitive-services-security.md)。

### <a name="february-2020"></a>2020 年 2 月

* 带 GenerateAnswer API 的 [NPM 包](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)

### <a name="november-2019"></a>2019 年 11 月

* 适用于 QnA Maker 的[美国政府云支持](https://docs.microsoft.com/azure/azure-government/documentation-government-services-aiandcognitiveservices#qna-maker)
* GA 中的[多轮次](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/multiturn-conversation)功能
* 第 1 层语言中可用的[闲聊支持](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base#language-support)

### <a name="october-2019"></a>2019 年 10 月

* 为 QnA Maker 服务中的所有知识库[显式设置语言](./how-to/language-knowledge-base.md#select-language-when-creating-first-knowledge-base)。

### <a name="september-2019"></a>2019 年 9 月

* 使用 [XLS 文件格式](concepts/content-types.md)导入和导出

### <a name="june-2019"></a>2019 年 6 月

* 改进了法语、意大利语、德语、西班牙语、葡萄牙语的[排名器模型](concepts/query-knowledge-base.md#ranker-process)

### <a name="april-2019"></a>2019 年 4 月

* 支持网站内容提取
* 通过经过身份验证的访问提供 [SharePoint 文档](how-to/add-sharepoint-datasources.md)支持

### <a name="march-2019"></a>2019 年 3 月

* [主动学习](how-to/improve-knowledge-base.md)根据实际用户问题为新问题备选方案提供建议
* 改进了英语自然语言处理 (NLP) [排名器](concepts/query-knowledge-base.md#ranker-process)模型

> [!div class="nextstepaction"]
> [创建 QnA Maker 服务](how-to/set-up-qnamaker-service-azure.md)

## <a name="cognitive-service-updates"></a>认知服务更新

[认知服务的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
