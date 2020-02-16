---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5accc38a8693fba5934b1182ebdafe8921540d98
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170205"
---
|名称 |说明 |效果 |版本 |
|---|---|---|---|
|[\[受限预览版\]：\[AKS\] 在 AKS 中不允许特权容器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerNoPrivilege_EnforceRegoPolicy.json) |此策略不允许在 Azure Kubernetes 服务群集中创建特权容器。 受限预览版策略仅适用于已注册的订阅。 若要注册，请访问 https://aka.ms/akspolicyonboarding 。 有关使用此策略的说明，请访问 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[受限预览版\]：\[AKS\] 在 AKS 中强制实施 HTTPS 入口](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/IngressHttpsOnly_EnforceRegoPolicy.json) |此策略在 Azure Kubernetes 服务群集中强制实施 HTTPS 入口。 受限预览版策略仅适用于已注册的订阅。 若要注册，请访问 https://aka.ms/akspolicyonboarding 。 有关使用此策略的说明，请访问 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[受限预览版\]：\[AKS\] 在 AKS 中强制实施内部负载均衡器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/LoadbalancerNoPublicIPs_EnforceRegoPolicy.json) |此策略强制要求负载均衡器在 Azure Kubernetes 服务群集中没有公共 IP。 受限预览版策略仅适用于已注册的订阅。 若要注册，请访问 https://aka.ms/akspolicyonboarding 。 有关使用此策略的说明，请访问 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[受限预览版\]：\[AKS\] 在 AKS 中的 pod 上强制实施标签](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/PodEnforceLabels_EnforceRegoPolicy.json) |此策略强制要求为 Azure Kubernetes 服务群集中的 pod 提供指定的标签。 受限预览版策略仅适用于已注册的订阅。 若要注册，请访问 https://aka.ms/akspolicyonboarding 。 有关使用此策略的说明，请访问 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[受限预览版\]：\[AKS\] 在 AKS 中强制实施跨命名空间的唯一入口主机名](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/IngressHostnamesConflict_EnforceRegoPolicy.json) |此策略在 Azure Kubernetes 服务群集中跨命名空间强制实施唯一的入口主机名。 受限预览版策略仅适用于已注册的订阅。 若要注册，请访问 https://aka.ms/akspolicyonboarding 。 有关使用此策略的说明，请访问 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[受限预览版\]：\[AKS\] 确保容器仅侦听 AKS 中允许的端口](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerAllowedPorts_EnforceRegoPolicy.json) |此策略强制要求容器仅侦听 Azure Kubernetes 服务群集中允许的端口。 受限预览版策略仅适用于已注册的订阅。 若要注册，请访问 https://aka.ms/akspolicyonboarding 。 有关使用此策略的说明，请访问 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[受限预览版\]：\[AKS\] 确保在 AKS 中的容器上定义 CPU 和内存资源限制](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerResourceLimits_EnforceRegoPolicy.json) |此策略确保在 Azure Kubernetes 服务群集中的容器上定义 CPU 和内存资源限制。 受限预览版策略仅适用于已注册的订阅。 若要注册，请访问 https://aka.ms/akspolicyonboarding 。 有关使用此策略的说明，请访问 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[受限预览版\]：\[AKS\] 确保 AKS 中只有允许的容器映像](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerAllowedImages_EnforceRegoPolicy.json) |此策略确保只在 Azure Kubernetes 服务群集中运行允许的容器映像。 受限预览版策略仅适用于已注册的订阅。 若要注册，请访问 https://aka.ms/akspolicyonboarding 。 有关使用此策略的说明，请访问 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
|[\[受限预览版\]：\[AKS\] 确保服务仅侦听 AKS 中允许的端口](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ServiceAllowedPorts_EnforceRegoPolicy.json) |此策略强制要求服务仅侦听 Azure Kubernetes 服务群集中允许的端口。 受限预览版策略仅适用于已注册的订阅。 若要注册，请访问 https://aka.ms/akspolicyonboarding 。 有关使用此策略的说明，请访问 https://aka.ms/akspolicydoc 。 |EnforceRegoPolicy、Disabled |1.0.0-preview |
