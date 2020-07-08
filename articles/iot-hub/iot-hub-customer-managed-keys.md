---
title: Azure IoT 中心通过客户托管的密钥进行静态数据加密 |Microsoft Docs
description: 通过 IoT 中心的客户托管密钥加密静态数据
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976568"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>通过 IoT 中心的客户托管密钥加密静态数据

IoT 中心支持静态数据加密与客户托管的密钥（CMK），也称为自带密钥（BYOK）。 Azure IoT 中心提供静态数据和传输中的数据加密，因为它是在我们的数据中心编写的，在你访问数据时对数据进行解密。 默认情况下，IoT 中心使用 Microsoft 托管的密钥来加密静态数据。 使用 CMK，可以根据默认加密获取另一层加密，还可以选择使用密钥加密密钥加密静态数据，并通过[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)进行管理。 这使你可以灵活地创建、轮换、禁用和撤消访问控制。 如果为 IoT 中心配置 BYOK，我们还提供双加密，这提供了另一层保护，同时允许你通过 Azure Key Vault 控制加密密钥。

此功能需要创建新的 IoT 中心（"基本" 或 "标准" 层）。 若要尝试此功能，请通过[Microsoft 支持](https://azure.microsoft.com/support/create-ticket/)联系我们。 联系 Microsoft 支持部门时，共享公司名称和订阅 ID。


## <a name="next-steps"></a>后续步骤

* [了解有关 IoT 中心的详细信息](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
