---
title: Azure IoT 中心通过客户管理的密钥进行静态数据加密| Microsoft Docs
description: 通过 IoT 中心的客户托管密钥加密静态数据
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84976568"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>通过 IoT 中心的客户托管密钥加密静态数据

IoT 中心支持使用客户托管的密钥 (CMK) 对静态数据加密，该密钥也称为自带密钥 (BYOK)。 Azure IoT 中心对写入数据中心的静态数据和传输中的数据进行加密，并在你访问数据时解密。 默认情况下，IoT 中心使用 Microsoft 托管的密钥来加密静态数据。 使用 CMK，可以在默认加密的基础上再添加一层加密，还可以选择使用密钥加密密钥（通过 [Azure密钥保管库](https://azure.microsoft.com/services/key-vault/)管理）来加密静态数据。 这使你能够灵活创建、轮换、禁用和撤销访问控制权。 如果为 IoT 中心配置了 BYOK，我们还提供双重加密，可提供额外一层保护，同时可通过 Azure 密钥保管库控制加密密钥。

此功能需要创建新的 IoT 中心（基本层或标准层）。 若要试用此功能，请通过 [Microsoft 支持](https://azure.microsoft.com/support/create-ticket/)人员与我们联系。 联系 Microsoft 支持人员时，请提供你的公司名称和订阅 ID。


## <a name="next-steps"></a>后续步骤

* [详细了解 IoT 中心](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [详细了解 Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
