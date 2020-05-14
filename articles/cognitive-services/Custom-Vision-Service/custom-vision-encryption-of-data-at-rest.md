---
title: 静态数据加密自定义视觉
titleSuffix: Azure Cognitive Services
description: 静态数据加密自定义视觉。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202267"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>静态数据加密自定义视觉

当你将数据保存到云中时，Azure 自定义视觉会自动对其进行加密。 自定义视觉加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 仅限11月 11 2020 日之后创建的客户托管密钥是可用的资源。 若要将 CMK 与自定义视觉一起使用，需要创建新的自定义视觉资源。 创建资源后，可以使用 Azure Key Vault 来设置托管标识。

### <a name="regional-availability"></a>区域可用性

客户托管的密钥目前在以下区域中提供：

* 美国中南部
* 美国西部 2
* 美国东部
* US Gov 弗吉尼亚州

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>后续步骤

* [自定义视觉客户管理的密钥请求窗体](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


