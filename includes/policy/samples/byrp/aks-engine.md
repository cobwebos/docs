---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ec5eecfd8a303b897f8e91beb16dcab2dcf07f8f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2020
ms.locfileid: "84704775"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[不允许 Kubernetes 群集中有特权容器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4) |此策略不允许在 Kubernetes 群集中创建特权容器。 有关使用此策略的说明，请访问 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint、disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege_EnforceOPAConstraint.json) |
|[在 Kubernetes 群集中强制实施 HTTPS 入口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |此策略在 Kubernetes 群集中强制实施 HTTPS 入口。 有关使用此策略的说明，请访问 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint、disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly_EnforceOPAConstraint.json) |
|[在 Kubernetes 群集中强制实施内部负载均衡器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3fc4dc25-5baf-40d8-9b05-7fe74c1bc64e) |此策略强制要求负载均衡器在 Kubernetes 群集中没有公共 IP。 有关使用此策略的说明，请访问 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint、disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/LoadbalancerNoPublicIPs_EnforceOPAConstraint.json) |
|[在 Kubernetes 群集中的 Pod 上强制实施标签](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F46592696-4c7b-4bf3-9e45-6c2763bdc0a6) |此策略强制要求为 Kubernetes 群集中的 pod 提供指定的标签。 有关使用此策略的说明，请访问 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint、disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/PodEnforceLabels_EnforceOPAConstraint.json) |
|[确保容器 CPU 和内存资源限制不超过 Kubernetes 群集中指定的限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe345eecc-fa47-480f-9e88-67dcc122b164) |此策略确保在 Kubernetes 群集中定义容器 CPU 和内存资源限制，且不超过指定的限制。 有关使用此策略的说明，请访问 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint、disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits_EnforceOPAConstraint.json) |
|[确保容器仅侦听 Kubernetes 群集中允许的端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F440b515e-a580-421e-abeb-b159a61ddcbc) |此策略强制要求容器仅侦听 Kubernetes 群集中允许的端口。 有关使用此策略的说明，请访问 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint、disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts_EnforceOPAConstraint.json) |
|[确保只有允许使用的容器映像才在 Kubernetes 群集中运行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffebd0533-8e55-448f-b837-bd0e06f16469) |此策略确保只在 Kubernetes 群集中运行允许的容器映像。 有关使用此策略的说明，请访问 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint、disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages_EnforceOPAConstraint.json) |
|[确保服务只在 Kubernetes 群集中侦听允许使用的端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F233a2a17-77ca-4fb1-9b6b-69223d272a44) |此策略强制要求服务仅侦听 Kubernetes 群集中允许的端口。 有关使用此策略的说明，请访问 [https://aka.ms/kubepolicydoc](https://aka.ms/kubepolicydoc)。 |enforceOPAConstraint、disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts_EnforceOPAConstraint.json) |
