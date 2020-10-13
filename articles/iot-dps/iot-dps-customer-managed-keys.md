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
ms.openlocfilehash: e9b5a549d550f117e080fc64193fbe29658f401d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85298219"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>通过设备预配服务的客户托管密钥加密静态数据

## <a name="overview"></a>概述

设备预配服务 (DPS) 支持静态数据与客户托管密钥 (CMK) ，也称为自带密钥 (BYOK) 。 DPS 提供静态数据和传输中的数据加密，因为它是在我们的数据中心编写的，并在您访问数据时对其进行解密。 默认情况下，DPS 使用 Microsoft 托管的密钥来加密静态数据。 使用 CMK，可以通过选择使用密钥加密密钥加密静态数据，并通过 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)进行管理，来在默认平台加密之上获取额外的加密层。 这使你可以灵活地创建、轮换、禁用和撤消密钥。 如果为你的 DPS 配置了 CMK，则意味着启用了双加密，并对数据进行了两层保护。 

此功能需要创建新的 DPS。 若要试用此功能，请通过 [Microsoft 支持](https://azure.microsoft.com/support/create-ticket/)人员与我们联系。 联系 Microsoft 支持人员时，请提供你的公司名称和订阅 ID。


## <a name="next-steps"></a>后续步骤

* [了解有关设备预配服务的详细信息](https://docs.microsoft.com/azure/iot-dps/)

* [详细了解 Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
