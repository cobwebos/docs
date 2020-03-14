---
title: 数据存储-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 将加密的数据存储在与密钥指定的区域对应的 Azure 数据存储中。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220013"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>语言理解 (LUIS) 认知服务中的数据存储和删除
LUIS 将加密的数据存储在与密钥指定的区域对应的 Azure 数据存储中。 此数据将存储 30 天。 

## <a name="export-and-delete-app"></a>导出和删除应用
用户对于[导出](luis-how-to-start-new-app.md#export-app)和[删除](luis-how-to-start-new-app.md#delete-app)应用拥有完全的控制权。 

## <a name="utterances"></a>陈述

最谈话可以存储在两个不同的位置。 

* 在**创作过程**中，会创建最谈话并将其存储在意向中。 最谈话是成功的 LUIS 应用所必需的。 发布应用并在终结点接收查询后，终结点请求的 querystring `log=false`确定是否存储终结点查询文本。 如果终结点已存储，它将成为活动学习最谈话的一部分，该部分位于门户的 "**生成**" 部分中的 "**查看终结点最谈话**" 部分中。 
* **查看终结点最谈话**并将查询文本添加到意向时，查询文本不再作为要查看的终结点最谈话的一部分存储。 它将添加到应用程序的意图。 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>从意向中删除示例最谈话

删除用于定型 [LUIS](luis-reference-regions.md) 的示例话语。 如果从 LUIS 应用中删除某个示例陈述，则会将其从 LUIS Web 服务中删除，导致其无法导出。

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>从活动学习中删除最谈话

可以从 LUIS 在 **“查看终结点陈述”页[中建议的用户陈述列表中删除陈述](luis-how-to-review-endpoint-utterances.md)** 。 从此列表中删除表述可以防止系统再将其作为建议提出来，但不会将其从日志中删除。

如果不需要主动学习最谈话，可以[禁用活动学习](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 禁用活动学习还会禁用日志记录。

### <a name="disable-logging-utterances"></a>禁用日志记录最谈话
[禁用活动学习](luis-how-to-review-endpoint-utterances.md#disable-active-learning)将禁用日志记录。


<a name="accounts"></a>

## <a name="delete-an-account"></a>删除帐户
如果删除某个帐户，则会删除所有应用及其示例表述和日志。 帐户和数据被永久删除之前，数据将保留 60 天。

从“设置”页可以删除帐户。 在右上角导航栏中选择帐户名可转到“设置”页。

## <a name="data-inactivity-as-an-expired-subscription"></a>数据处于非活动状态会被视为过期订阅
出于数据保留和删除的原因，Microsoft 有权自行将处于非活动状态的 LUIS 应用视为过期订阅。 如果应用在过去 90 天内满足以下条件，则将被视为处于非活动状态： 

* 未对应用发出任何调用。
* 未进行修改。
* 未分配有当前密钥。
* 无任何用户登录应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解导出和删除应用](luis-how-to-start-new-app.md)
