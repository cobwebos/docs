---
title: 静态数据的 Personalizer 服务加密
titleSuffix: Azure Cognitive Services
description: 静态数据的 Personalizer 服务加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80071085"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>静态数据的 Personalizer 服务加密

当你将数据保存到云时，Personalizer 服务会自动对其进行加密。 Personalizer 服务加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户托管的密钥仅可用于 E0 定价层。 若要请求使用客户管理的密钥的功能，请填写并提交[Personalizer 服务客户托管的密钥请求表单](https://aka.ms/cogsvc-cmk)。 大约需要3-5 个工作日内就会收到请求的状态。 根据需要，你可以将置于队列中并在空间可用时进行批准。 批准使用 CMK 与 Personalizer 服务后，将需要创建新的 Personalizer 资源，并选择 E0 作为定价层。 创建具有 E0 定价层的 Personalizer 资源后，可以使用 Azure Key Vault 来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>后续步骤

* [Personalizer 服务客户托管的密钥请求窗体](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
