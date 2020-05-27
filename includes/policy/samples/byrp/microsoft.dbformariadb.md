---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2378c49d5b2c95ffad3a1b320521d77b4ab59e2e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656793"
---
|名称 |说明 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[应为 Azure Database for MariaDB 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |此策略将审核未启用异地冗余备份的任何 Azure Database for MariaDB。 |Audit、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json)。 |
|[MariaDB 服务器应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |此策略审核未配置为使用虚拟网络服务终结点的 MariaDB 服务器。 有关更多详细信息，请访问 [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork)。 |AuditIfNotExists、Disabled |1.0.1 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json)。 |
|[应为 MariaDB 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |此策略审核未配置为使用专用终结点的 MariaDB 服务器。 有关更多详细信息，请访问 [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink)。 |AuditIfNotExists、Disabled |1.0.1 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json)。 |
|[应为 MariaDB 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |此策略审核你的环境中启用了公用网络访问的 MariaDB 服务器。 有关更多详细信息，请访问 [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542)。 |Audit、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json)。 |
