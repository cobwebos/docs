---
title: 数据存储
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 将加密的数据存储在与密钥指定的区域对应的 Azure 数据存储中。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: df8af9dc86e26a073b76bff33f35bad2ba32b5e8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105744"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>语言理解 (LUIS) 认知服务中的数据存储和删除
LUIS 将加密的数据存储在与密钥指定的区域对应的 Azure 数据存储中。 此数据将存储 30 天。 

## <a name="export-and-delete-app"></a>导出和删除应用
用户对于[导出](luis-how-to-start-new-app.md#export-app)和[删除](luis-how-to-start-new-app.md#delete-app)应用拥有完全的控制权。 

## <a name="utterances-in-an-intent"></a>意向的陈述
删除用于定型 [LUIS](luis-reference-regions.md) 的示例话语。 如果从 LUIS 应用中删除某个示例陈述，则会将其从 LUIS Web 服务中删除，导致其无法导出。

## <a name="utterances-in-review"></a>评审中的陈述
可以从 LUIS 在[“查看终结点陈述”页](luis-how-to-review-endoint-utt.md)中建议的用户陈述列表中删除陈述。 从此列表中删除表述可以防止系统再将其作为建议提出来，但不会将其从日志中删除。

## <a name="accounts"></a>帐户
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