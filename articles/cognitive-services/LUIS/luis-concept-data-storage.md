---
title: 数据存储
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 将加密的数据存储在与密钥指定的区域对应的 Azure 数据存储中。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893191"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>语言理解 (LUIS) 认知服务中的数据存储和删除
LUIS 将加密的数据存储在与密钥指定的区域对应的 Azure 数据存储中。 此数据将存储 30 天。 

## <a name="export-and-delete-app"></a>导出和删除应用
用户对于[导出](luis-how-to-start-new-app.md#export-app)和[删除](luis-how-to-start-new-app.md#delete-app)应用拥有完全的控制权。 

## <a name="utterances"></a>陈述

可以在两个不同位置存储语音样本。 

* 期间**创作过程**，创建并存储在意图中语音样本。 需要成功的 LUIS 应用程序中意向的语音样本。 应用程序发布并收到在终结点，终结点请求的查询字符串的查询后`log=false`，确定是否存储有终结点查询文本。 如果存储终结点，它将成为一部分中找到的主动学习语音样本**构建**门户的一部分，在**查看终结点语音样本**部分。 
* 当您**查看终结点语音样本**，并将查询文本添加到意向，将语音样本不再存储为终结点语音样本，若要检查的一部分。 它将添加到应用程序的意向。 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>意向从删除的示例查询文本
删除用于定型 [LUIS](luis-reference-regions.md) 的示例话语。 如果从 LUIS 应用中删除某个示例陈述，则会将其从 LUIS Web 服务中删除，导致其无法导出。

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>删除语音样本中查看从主动学习

可以从 LUIS 在[“查看终结点陈述”页](luis-how-to-review-endpoint-utterances.md)中建议的用户陈述列表中删除陈述。 从此列表中删除表述可以防止系统再将其作为建议提出来，但不会将其从日志中删除。

如果不想主动学习语音样本，则可以[禁用主动学习](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 禁用主动学习也会禁用日志记录。

### <a name="disable-logging-utterances"></a>禁用日志记录查询文本
[禁用主动学习](luis-how-to-review-endpoint-utterances.md#disable-active-learning)禁用日志记录。


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
> [了解有关导出和删除应用](luis-how-to-start-new-app.md)
