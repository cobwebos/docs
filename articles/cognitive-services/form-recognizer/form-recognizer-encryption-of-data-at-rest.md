---
title: 表单识别器静态数据的服务加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你通过自己的密钥（称为客户托管密钥 (CMK) ）管理你的认知服务订阅。 本文介绍窗体识别器的静态数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 86b15b0059c2e3466ef65daeb53780798b3882d0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079245"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>格式识别静态数据的加密

将数据保存到云时，Azure 表单识别器会自动加密数据。 表单识别器加密可保护你的数据，并帮助你满足组织的安全性和符合性承诺。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 仅限11月 11 2020 日之后创建的客户托管密钥是可用的资源。 若要将 CMK 与窗体识别器结合使用，需要创建新的窗体识别器资源。 创建资源后，可以使用 Azure Key Vault 来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* [窗体识别器客户托管的密钥请求窗体](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)