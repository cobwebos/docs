---
title: 静态数据面服务加密
titleSuffix: Azure Cognitive Services
description: 对静态数据进行面对面服务加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372212"
---
# <a name="face-service-encryption-of-data-at-rest"></a>静态数据面服务加密

Face 服务在将数据保存到云中时自动加密数据。 Face 服务加密可保护您的数据，并帮助您履行组织安全和合规性承诺。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户管理的密钥仅在 E0 定价层上可用。 要请求使用客户管理的密钥，请填写并提交[Face 服务客户管理密钥请求表](https://aka.ms/cogsvc-cmk)。 大约需要 3-5 个工作日才能回复您的请求状态。 根据需求，您可能会被放置在队列中，并在可用空间时获得批准。 一旦批准将 CMK 与面服务一起使用，您将需要创建新的面容资源，并选择 E0 作为定价层。 创建具有 E0 定价层的 Face 资源后，可以使用 Azure 密钥保管库来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>后续步骤

* [面对面服务客户管理的关键请求表](https://aka.ms/cogsvc-cmk)
* [了解有关 Azure 密钥保管库的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


