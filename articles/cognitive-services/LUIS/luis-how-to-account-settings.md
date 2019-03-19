---
title: 管理帐户和密钥
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 帐户信息的两个关键部分是用户帐户和创作密钥。 Account.microsoft.com 管理您的登录信息。 你创作的密钥管理在 LUIS 门户设置页。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: d7d63ad642ab2d3b336e15dcca606077762ceb9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116616"
---
# <a name="manage-account-and-authoring-key"></a>管理帐户和创作密钥

LUIS 帐户信息的两个关键部分是用户帐户和创作密钥。 登录信息在 [account.microsoft.com](https://account.microsoft.com) 上进行管理。 你创作的密钥管理从[LUIS](luis-reference-regions.md)门户**设置**页。

## <a name="authoring-key"></a>创作密钥

此创作单一、 特定于区域的密钥，在**设置**页上，可创建所有的应用，从[LUIS](luis-reference-regions.md)门户，以及[创作 Api](https://aka.ms/luis-authoring-api)。 为方便起见，创作密钥每月可执行[有限](luis-boundaries.md)数量的终结点查询。

[![LUIS 设置页](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

创作密钥可用于你拥有的所有应用以及列为协作者的任何应用。

## <a name="authoring-key-regions"></a>创作密钥区域

创作密钥特定于[创作区域](luis-reference-regions.md#publishing-regions)。 此密钥不可用于其他区域。

## <a name="reset-authoring-key"></a>重置创作密钥

如果创作密钥遭到泄露，请重置密钥。 密钥在所有应用上重置[LUIS](luis-reference-regions.md)门户。 如果通过创作 API 创作应用，则需要将 `Ocp-Apim-Subscription-Key` 的值更改为新密钥。

## <a name="delete-account"></a>删除帐户

要了解删除帐户时一并删除了哪些数据，请参阅[数据存储和删除](luis-concept-data-storage.md#accounts)。

## <a name="next-steps"></a>后续步骤

深入了解[创作密钥](luis-concept-keys.md#authoring-key)。

