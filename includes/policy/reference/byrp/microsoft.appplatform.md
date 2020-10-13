---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 87aab0d28d17a0286ae48fb899f878303b43fe8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818884"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核未启用分布式跟踪的 Azure Spring Cloud 实例](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |可以通过 Azure Spring Cloud 中的分布式跟踪工具调试和监视应用程序中微服务之间的复杂互连。 分布式跟踪工具应已启用并处于正常状态。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud 应使用网络注入](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud 实例应使用虚拟网络注入实现以下目的：1. 将 Azure Spring Cloud 与 Internet 隔离。 2. 使 Azure Spring Cloud 能够与本地数据中心内的系统和/或其他虚拟网络中的 Azure 服务内的系统进行交互。 3. 授权客户控制 Azure Spring Cloud 的入站和出站网络通信。 |审核、已禁用、拒绝 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
