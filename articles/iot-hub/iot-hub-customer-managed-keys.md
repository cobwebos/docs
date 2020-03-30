---
title: Azure IoT 中心数据通过客户管理的密钥进行静态加密*微软文档
description: 使用 IoT 中心的客户托管密钥对静态数据进行加密
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370570"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>使用 IoT 中心的客户托管密钥对静态数据进行加密

IoT 中心使用客户管理的密钥 （CMK）支持静态数据加密，也称为自带密钥 （BYOK），支持 Azure IoT 中心。 Azure IoT 中心提供静态和传输中的数据加密。 默认情况下，IoT 中心使用 Microsoft 管理的密钥对数据进行加密。 借助 CMK 支持，客户现在可以选择使用[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)使用由客户管理的密钥加密密钥来加密静态数据。

此功能需要在以下区域之一（美国东部、美国西部 2、美国中南部或美国政府）创建新的 IoT 中心（基本或标准层）。 要尝试此功能，请通过[Microsoft 支持](https://azure.microsoft.com/support/create-ticket/)与我们联系。 联系 Microsoft 支持部门时，请共享公司名称和订阅 ID。

## <a name="next-steps"></a>后续步骤

* [了解有关 IoT 中心](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [了解有关 Azure 密钥保管库的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
