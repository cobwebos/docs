---
title: Azure IoT 中心通过客户托管的密钥进行静态数据加密 |Microsoft Docs
description: 通过 IoT 中心的客户托管密钥加密静态数据
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370570"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>通过 IoT 中心的客户托管密钥加密静态数据

IoT 中心支持静态数据和客户管理的密钥（CMK）（也称为自带密钥（BYOK））支持 Azure IoT 中心。 Azure IoT 中心提供静态数据和传输中的数据加密。 默认情况下，IoT 中心使用 Microsoft 托管的密钥来加密数据。 使用 CMK 支持，客户现在可以选择使用[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)的客户管理的密钥加密密钥来加密静态数据。

此功能需要在以下区域之一创建新的 IoT 中心（基本或标准层）：美国东部、美国西部2、美国中南部或 US Gov。 若要尝试此功能，请通过[Microsoft 支持](https://azure.microsoft.com/support/create-ticket/)联系我们。 联系 Microsoft 支持部门时，共享公司名称和订阅 ID。

## <a name="next-steps"></a>后续步骤

* [了解有关 IoT 中心的详细信息](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
