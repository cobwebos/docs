---
title: Azure 设备预配服务数据通过客户管理的密钥进行静态加密*微软文档
description: 使用客户管理的密钥对静态数据进行加密，以便进行设备配置服务
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675138"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>使用客户管理的密钥对静态数据进行加密，以便进行设备配置服务

## <a name="overview"></a>概述

设备配置服务 （DPS） 支持使用客户管理的密钥 （CMK）对静态数据进行加密，也称为自带密钥 （BYOK）。 DPS 提供静态和传输中的数据加密。 默认情况下，DPS 使用 Microsoft 管理的密钥对数据进行加密。 借助 CMK 支持，客户现在可以选择使用[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)使用由客户管理的密钥加密密钥来加密静态数据。

此功能要求在以下区域之一（美国东部、美国西部 2 或美国中南部）创建新的 DPS。 要尝试此功能，请通过[Microsoft 支持](https://azure.microsoft.com/support/create-ticket/)与我们联系。 联系 Microsoft 支持部门时，请共享公司名称和订阅 ID。

## <a name="next-steps"></a>后续步骤

* [了解有关设备预配服务的信息](https://docs.microsoft.com/azure/iot-dps/)

* [了解有关 Azure 密钥保管库的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)