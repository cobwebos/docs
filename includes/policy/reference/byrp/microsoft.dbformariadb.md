---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c4619610cc8e54f9fbe556e3d12668a680a2e261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859752"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为 Azure Database for MariaDB 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB 允许你为数据库服务器选择 "冗余" 选项。 它可以设置为异地冗余备份存储，其中的数据不仅存储在承载服务器的区域内，而且还会复制到配对区域，以便在发生区域故障时提供恢复选项。 仅允许在服务器创建过程中为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB 服务器应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |基于虚拟网络的防火墙规则用于启用从特定子网到 Azure Database for MariaDB 的流量，同时确保流量保持在 Azure 边界内。 此策略提供了一种方法来审核 Azure Database for MariaDB 是否具有正在使用的虚拟网络服务终结点。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[应为 MariaDB 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |专用终结点连接通过启用到 Azure Database for MariaDB 的专用连接来强制安全通信。 将专用终结点连接配置为仅允许来自已知网络的流量的访问，并防止从所有其他 IP 地址（包括 Azure 中）进行访问。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[应为 MariaDB 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |禁用公共网络访问属性可以确保只能从专用终结点访问 Azure Database for MariaDB，从而提高安全性。 此配置会严格禁止从 Azure IP 范围之外的任何公用地址空间进行访问，并拒绝与基于 IP 或虚拟网络的防火墙规则匹配的所有登录名。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
