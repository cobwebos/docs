---
title: 管理设置
titleSuffix: Language Understanding - Azure Cognitive Services
description: 使用 LUIS 网站管理用户帐户设置及用于你的所有应用的创作密钥。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: bd6ae88834b45e9e154eb1e5e3ba921f403c7eaa
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138738"
---
# <a name="manage-account-and-authoring-key"></a>管理帐户和创作密钥
LUIS 帐户信息的两个关键部分是用户帐户和创作密钥。 登录信息在 [account.microsoft.com](https://account.microsoft.com) 上进行管理。 创作密钥在 [LUIS](luis-reference-regions.md) 网站的“设置”页面上进行管理。 

## <a name="authoring-key"></a>创作密钥

使用“设置”页面上的这个区域特定的单个创作密钥，可通过 [LUIS](luis-reference-regions.md) 网站和[创作 API](https://aka.ms/luis-authoring-api) 创作各种应用。 为方便起见，创作密钥每月可执行[有限](luis-boundaries.md)数量的终结点查询。 

[![LUIS 设置页](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

创作密钥可用于你拥有的所有应用以及列为协作者的任何应用。

## <a name="authoring-key-regions"></a>创作密钥区域
创作密钥特定于[创作区域](luis-reference-regions.md#publishing-regions)。 此密钥不可用于其他区域。 

## <a name="reset-authoring-key"></a>重置创作密钥
如果创作密钥遭到泄露，请重置密钥。 可在 [LUIS](luis-reference-regions.md) 网站的任意应用中重置此密钥。 如果通过创作 API 创作应用，则需要将 `Ocp-Apim-Subscription-Key` 的值更改为新密钥。 

## <a name="delete-account"></a>删除帐户
要了解删除帐户时一并删除了哪些数据，请参阅[数据存储和删除](luis-concept-data-storage.md#accounts)。 

## <a name="next-steps"></a>后续步骤

深入了解[创作密钥](luis-concept-keys.md#authoring-key)。 

