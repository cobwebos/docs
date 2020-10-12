---
title: 指标顾问服务加密
titleSuffix: Azure Cognitive Services
description: 指标 Advisor 服务静态数据的加密。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 9d6a2f8a69d41d29e635b03425ed738484d6f408
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934297"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>指标 Advisor 服务静态数据的加密

将数据保存到云时，指标 Advisor 服务会自动加密数据。 指标顾问服务加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户托管的密钥仅可用于 E0 定价层。 若要请求使用客户管理的密钥的功能，请填写并提交 [指标 Advisor 服务 Customer-Managed 密钥请求表单](https://aka.ms/cogsvc-cmk)。 大约需要3-5 个工作日内就会收到请求的状态。 根据需要，你可以将置于队列中并在空间可用时进行批准。 批准使用 CMK 与指标顾问服务后，将需要创建新的指标顾问资源，并选择 E0 作为定价层。 创建具有 E0 定价层的指标顾问资源后，可以使用 Azure Key Vault 来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* [指标顾问服务 Customer-Managed 密钥请求表单](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
