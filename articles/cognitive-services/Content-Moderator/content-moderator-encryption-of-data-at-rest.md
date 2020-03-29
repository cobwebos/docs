---
title: 静态数据的内容管理员加密
titleSuffix: Azure Cognitive Services
description: 内容审阅人对静态数据进行加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372157"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>静态数据的内容管理员加密

内容审阅人会在数据保存到云中时自动加密数据，从而有助于实现组织安全性和合规性目标。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户管理的密钥仅在 E0 定价层上可用。 要请求使用客户管理的密钥，请填写并提交[内容审阅人客户管理密钥请求表](https://aka.ms/cogsvc-cmk)。 大约需要 3-5 个工作日才能回复您的请求状态。 根据需求，您可能会被放置在队列中，并在可用空间时获得批准。 一旦批准将 CMK 与内容审阅人服务一起使用，您将需要创建新的内容审阅人资源，并选择 E0 作为定价层。 创建具有 E0 定价层的内容审阅人资源后，可以使用 Azure 密钥保管库来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>为您的内容审阅者团队启用数据加密

要为内容审阅人审阅团队启用数据加密，请参阅[快速入门：尝试 Web 上的内容审阅者](quick-start.md#create-a-review-team)。  

> [!NOTE]
> 您需要提供包含内容审阅人 E0 定价层_的资源 ID。_


## <a name="next-steps"></a>后续步骤

* [内容审阅人客户管理的关键请求表](https://aka.ms/cogsvc-cmk)
* [了解有关 Azure 密钥保管库的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
