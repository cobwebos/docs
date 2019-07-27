---
title: 管理帐户和密钥-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 帐户信息的两个关键部分是用户帐户和创作密钥。 登录信息在 account.microsoft.com 上进行管理。 创作密钥在 LUIS 门户的“设置”页面上进行管理。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 9164c94886488ac7dcadeb7894453daad5d8b322
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560488"
---
# <a name="manage-account-and-authoring-key"></a>管理帐户和创作密钥

LUIS 帐户信息的两个关键部分是用户帐户和创作密钥。 登录信息在 [account.microsoft.com](https://account.microsoft.com) 上进行管理。 创作密钥在 [LUIS](luis-reference-regions.md) 门户的“设置”页面上进行管理。

## <a name="authoring-key"></a>创作密钥

使用“设置”页面上的这个区域特定的单个创作密钥，可通过 [LUIS](luis-reference-regions.md) 门户和[创作 API](https://go.microsoft.com/fwlink/?linkid=2092087) 创作所有应用。 为方便起见，创作密钥每月可执行[有限](luis-boundaries.md)数量的终结点查询。

[![LUIS 设置页](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

创作密钥可用于你拥有的所有应用以及列为协作者的任何应用。

## <a name="authoring-key-regions"></a>创作密钥区域

创作密钥特定于[创作区域](luis-reference-regions.md#publishing-regions)。 此密钥不可用于其他区域。

## <a name="reset-authoring-key"></a>重置创作密钥

如果创作密钥遭到泄露，请重置密钥。 可在 [LUIS](luis-reference-regions.md) 门户的任意应用中重置此密钥。 如果通过创作 API 创作应用，则需要将 `Ocp-Apim-Subscription-Key` 的值更改为新密钥。

## <a name="delete-account"></a>删除帐户

要了解删除帐户时一并删除了哪些数据，请参阅[数据存储和删除](luis-concept-data-storage.md#accounts)。

## <a name="next-steps"></a>后续步骤

深入了解[创作密钥](luis-concept-keys.md#authoring-key)。

