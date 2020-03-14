---
title: 静态数据加密内容审查器
titleSuffix: Azure Cognitive Services
description: 静态数据加密内容审查器。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372157"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>静态数据加密内容审查器

内容审查器在将数据保存到云时，会自动对其进行加密，从而有助于满足组织的安全性和符合性目标。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户托管的密钥仅可用于 E0 定价层。 若要请求使用客户管理的密钥的功能，请填写并提交[内容审查器客户管理的密钥请求窗体](https://aka.ms/cogsvc-cmk)。 大约需要3-5 个工作日内就会收到请求的状态。 根据需要，你可以将置于队列中并在空间可用时进行批准。 批准使用 CMK 与内容审查器服务后，将需要创建新的内容审查器资源，并选择 E0 作为定价层。 创建具有 E0 定价层的内容审查器资源后，可以使用 Azure Key Vault 设置托管标识。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>为内容审查器团队启用数据加密

若要为内容审查器审查团队启用数据加密，请参阅[快速入门：在 web 上内容审查器](quick-start.md#create-a-review-team)。  

> [!NOTE]
> 需要提供具有内容审查器 E0 定价层的_资源 ID_ 。


## <a name="next-steps"></a>后续步骤

* [内容审查器客户管理的密钥请求窗体](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
