---
title: 静态数据的人脸服务加密
titleSuffix: Azure Cognitive Services
description: 静态数据的人脸服务加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372212"
---
# <a name="face-service-encryption-of-data-at-rest"></a>静态数据的人脸服务加密

在将数据保存到云时，人脸服务会自动加密数据。 人脸服务加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户托管的密钥仅可用于 E0 定价层。 若要请求使用客户管理的密钥的功能，请填写并提交[面部服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)。 大约需要3-5 个工作日内就会收到请求的状态。 根据需要，你可以将置于队列中并在空间可用时进行批准。 批准使用 CMK 与人脸服务后，你将需要创建新的人脸资源，并选择 E0 作为定价层。 创建具有 E0 定价层的面部资源后，可以使用 Azure Key Vault 来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>后续步骤

* [人脸服务客户托管的密钥请求窗体](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


