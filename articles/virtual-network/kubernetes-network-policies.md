---
title: Azure Kubernetes 网络策略 | Microsoft Docs
description: 了解用于确保 Kubernetes 群集安全的 Kubernetes 网络策略。
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159697"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes 网络策略概述

网络策略为 Pod 提供微分段，就像网络安全组 (NSG) 为 VM 提供微分段一样。 Azure 网络策略实现支持标准的 Kubernetes 网络策略规范。 可以使用标签来选择一组 Pod 并定义入口和出口规则的列表，这些规则指定允许从这些 Pod 出入的流量的类型。 在 [Kubernetes 文档](https://kubernetes.io/docs/concepts/services-networking/network-policies/)中详细了解 Kubernetes 网络策略。

![Kubernetes 网络策略概述](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure 网络策略可以与为容器提供 VNet 集成的 Azure CNI 配合使用。 目前仅 Linux 节点支持它。 这些实现根据定义的策略来配置 Linux IP 表规则，以便强制流量筛选。

## <a name="planning-security-for-your-kubernetes-cluster"></a>规划 Kubernetes 群集的安全性
在实现群集的安全性时，请使用网络安全组（Nsg）来筛选北南部流量，即进入和离开群集子网的流量，并将 Kubernetes 网络策略用于东-西流量，即群集。

## <a name="using-azure-kubernetes-network-policies"></a>使用 Azure Kubernetes 网络策略
Azure 网络策略可以通过下述方式来使用，以便为 Pod 提供微分段。

### <a name="acs-engine"></a>ACS-engine
ACS-Engine 是一项工具，可以生成 Azure 资源管理器模板，以便在 Azure 中部署 Kubernetes 群集。 群集配置在 JSON 文件中指定，该文件在生成模板时传递给工具。 若要详细了解受支持的群集设置及其说明的完整列表，请参阅“Microsoft Azure 容器服务引擎 - 群集定义”。

若要在使用 acs-engine 部署的群集上启用策略，请在群集定义文件中将 networkPolicy 设置的值指定为“azure”。

#### <a name="example-configuration"></a>示例配置

下面的 JSON 示例配置使用 Azure CNI 创建了一个新的虚拟网络和子网，并在其中部署了 Kubernetes 群集。 建议使用“记事本”来编辑此 JSON 文件。 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>在 Azure 中创建自己的 Kubernetes 群集
可以使用此实现在自行部署的 Kubernetes 群集中为 Pod 提供网络策略，不需依赖 ACS-Engine 之类的工具。 在此示例中，请先安装 CNI 插件，然后在群集中的每个虚拟机上启用它。 如需详细说明，请参阅[为自行部署的 Kubernetes 群集部署插件](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster)。

部署群集以后，请运行下面的 `kubectl` 命令，以便下载 Azure 网络策略 *daemonset* 并将其应用到群集。

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
此解决方案也是开源的，代码在 [Azure 容器网络存储库](https://github.com/Azure/azure-container-networking/tree/master/npm)中提供。



## <a name="next-steps"></a>后续步骤
- 了解 [Azure Kubernetes 服务](../aks/intro-kubernetes.md)。
-  了解[容器网络](container-networking-overview.md)。
- 为 Kubernetes 群集或 Docker 容器[部署插件](deploy-container-networking.md)。
