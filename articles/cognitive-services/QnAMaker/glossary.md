---
title: 术语表 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 术语表
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e28cddec005cb6ba99b9f60d8b03a11f1bc97062
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366303"
---
# <a name="glossary"></a>术语表

## <a name="qna-maker-service"></a>QnA Maker 服务
QnA Maker 服务是开始使用 QnA Maker 的先决条件。 购买 QnA Maker 层会在 Azure 订阅中设置资源，以创建和管理知识库。 每个 QnA Maker 用户帐户都可以在其 Azure 订阅中创建多个 QnA Maker 服务。

## <a name="knowledge-base"></a>知识库
知识库是通过 QnA Maker 创建、维护和使用的问题和答案的存储库。 每个 QnA Maker 层均可用于多个知识库。

## <a name="endpoint"></a>终结点
为知识库内容提供服务的基于 REST 的 HTTP 终结点，可以集成到应用程序（通常是聊天机器人）中。 

## <a name="test-knowledge-base"></a>测试知识库
知识库有两种状态 -“测试”和“已发布”。 测试知识库是正在针对响应的准确性和完整性编辑、保存和测试的版本。 对测试知识库所做的更改不会影响应用程序/聊天机器人的最终用户。

## <a name="published-knowledge-base"></a>已发布的知识库
知识库有两种状态 -“测试”和“已发布”。  已发布的知识库是聊天机器人/应用程序中使用的版本。 发布知识库的操作会将测试知识库的内容置于知识库的已发布版本中。 由于已发布的知识库是应用程序通过终结点使用的版本，因此应注意确保内容正确且经过良好测试。

## <a name="query"></a>查询
用户查询是最终用户或测试人员询问的知识库问题。 查询通常采用自然语言格式，或者为表示问题的几个关键字。

## <a name="response"></a>响应
响应是基于给定用户查询的最佳匹配从知识库检索的答案。

## <a name="confidence-score"></a>置信度分数
响应的置信度分数是介于 0 和 100 之间的数值，100 是指用户查询与知识库中的问题之间精确查询匹配，所提供的响应是对给定用户查询的正确、适当的响应。 答案通常按置信度分数排名，而置信度分数较高的答案则作为默认响应。
