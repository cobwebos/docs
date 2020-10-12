---
title: 静态数据加密自定义视觉
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍了自定义视觉静态的数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 39257419f179bdce8c94f2ddb3a7cd8f5ac2d34f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077749"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>静态数据加密自定义视觉

当你将数据保存到云中时，Azure 自定义视觉会自动对其进行加密。 自定义视觉加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 仅限11月 11 2020 日之后创建的客户托管密钥是可用的资源。 若要将 CMK 与自定义视觉一起使用，需要创建新的自定义视觉资源。 创建资源后，可以使用 Azure Key Vault 来设置托管标识。

## <a name="regional-availability"></a>区域可用性

客户托管的密钥目前在以下区域中提供：

* 美国中南部
* 美国西部 2
* 美国东部
* US Gov 弗吉尼亚州

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* 有关支持 CMK 的服务的完整列表，请参阅 [认知服务的客户托管密钥](../encryption/cognitive-services-encryption-keys-portal.md)
* [什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [认知服务 Customer-Managed 密钥请求表单](https://aka.ms/cogsvc-cmk)
