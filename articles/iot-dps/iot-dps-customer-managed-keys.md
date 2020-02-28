---
title: Azure 设备预配服务通过客户托管的密钥进行静态数据加密 |Microsoft Docs
description: 通过设备预配服务的客户托管密钥加密静态数据
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675138"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>通过设备预配服务的客户托管密钥加密静态数据

## <a name="overview"></a>概述

设备预配服务（DPS）支持通过客户托管的密钥（CMK）加密静态数据，也称为自带密钥（BYOK）。 DPS 提供静态数据和传输中的数据加密。 默认情况下，DPS 使用 Microsoft 托管的密钥来加密数据。 使用 CMK 支持，客户现在可以选择使用[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)的客户管理的密钥加密密钥来加密静态数据。

此功能需要在以下区域之一创建新的 DPS：美国东部、美国西部2或美国中南部。 若要尝试此功能，请通过[Microsoft 支持](https://azure.microsoft.com/support/create-ticket/)联系我们。 联系 Microsoft 支持部门时，共享公司名称和订阅 ID。

## <a name="next-steps"></a>后续步骤

* [了解有关设备预配服务的详细信息](https://docs.microsoft.com/azure/iot-dps/)

* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)