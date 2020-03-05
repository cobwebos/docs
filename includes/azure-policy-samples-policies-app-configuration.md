---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: 9e5b5d8b7b99134deba2df9b996b96e2bf904937
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262688"
---
|名称 |说明 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[应用程序配置应使用客户管理的密钥](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |此策略审核任何不使用客户管理的密钥的应用程序配置实例。 |Audit、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json)
|[应用程序配置应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |此策略审核任何不使用专用链接的应用程序配置实例。 |AuditIfNotExists、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json)
