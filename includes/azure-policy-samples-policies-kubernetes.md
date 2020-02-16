---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 49ba27ef559748a8805160558d48b7b7c2cbe80f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170155"
---
|名称 |说明 |效果 |版本 |
|---|---|---|---|
|[\[预览版\]：\[AKS 引擎\] 在 Kubernetes 群集中不允许特权容器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege_EnforceOPAConstraint.json) |此策略不允许在 Kubernetes 群集中创建特权容器。 有关使用此策略的说明，请访问 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[预览版\]：\[AKS 引擎\] 在 Kubernetes 群集中强制实施 HTTPS 入口](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly_EnforceOPAConstraint.json) |此策略在 Kubernetes 群集中强制实施 HTTPS 入口。 有关使用此策略的说明，请访问 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[预览版\]：\[AKS 引擎\] 在 Kubernetes 群集中强制实施内部负载均衡器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/LoadbalancerNoPublicIPs_EnforceOPAConstraint.json) |此策略强制要求负载均衡器在 Kubernetes 群集中没有公共 IP。 有关使用此策略的说明，请访问 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[预览版\]：\[AKS 引擎\] 在 Kubernetes 群集中的 pod 上强制实施标签](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/PodEnforceLabels_EnforceOPAConstraint.json) |此策略强制要求为 Kubernetes 群集中的 pod 提供指定的标签。 有关使用此策略的说明，请访问 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[预览版\]：\[AKS 引擎\] 在 Kubernetes 群集中跨命名空间强制实施唯一的入口主机名](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/IngressHostnamesConflict_EnforceOPAConstraint.json) |此策略在 Kubernetes 群集中跨命名空间强制实施唯一的入口主机名。 有关使用此策略的说明，请访问 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[预览版\]：\[AKS 引擎\] 确保 Kubernetes 群集中的容器 CPU 和内存资源限制不超过指定的限制](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits_EnforceOPAConstraint.json) |此策略确保在 Kubernetes 群集中定义容器 CPU 和内存资源限制，且不超过指定的限制。 有关使用此策略的说明，请访问 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[预览版\]：\[AKS 引擎\] 确保容器仅侦听 Kubernetes 群集中允许的端口](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts_EnforceOPAConstraint.json) |此策略强制要求容器仅侦听 Kubernetes 群集中允许的端口。 有关使用此策略的说明，请访问 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[预览版\]：\[AKS 引擎\] 确保 Kubernetes 群集中只有允许的容器映像](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages_EnforceOPAConstraint.json) |此策略确保只在 Kubernetes 群集中运行允许的容器映像。 有关使用此策略的说明，请访问 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint、disabled |1.0.0-preview |
|[\[预览版\]：\[AKS 引擎\] 确保服务仅侦听 Kubernetes 群集中允许的端口](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts_EnforceOPAConstraint.json) |此策略强制要求服务仅侦听 Kubernetes 群集中允许的端口。 有关使用此策略的说明，请访问 https://aka.ms/kubepolicydoc 。 |enforceOPAConstraint、disabled |1.0.0-preview |
