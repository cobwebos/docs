---
title: Azure Kubernetes 服务中支持的 Kubernetes 版本
description: 了解 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 版本支持策略和群集生命周期
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: a7b3176e39ccaa0f9ddb1ef45c33ec6902e62f1c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956324"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中支持的 Kubernetes 版本

Kubernetes 社区大约会每隔三个月发布次要版本。 这些版本包括新增功能和改进。 修补程序版本更为频繁（有时会每周发布），并且仅用于次要版本中的关键 Bug 修复。 这些修补程序版本包括针对影响大量客户以及在生产中基于 Kubernetes 运行的产品的安全漏洞或重大 Bug 的修复。

[aks-engine][aks-engine] 在第一天提供了新的 Kubernetes 次要版本。 AKS 服务级别目标 (SLO) 将根据版本的稳定性在 30 天内发布 AKS 群集的次要版本。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支持策略

AKS 支持以下四个 Kubernetes 次要版本：

- 上游发布的当前次要版本 (n)
- 以前的三个次要版本。 每个受支持的次要版本还支持两个稳定的修补程序。

例如，如果引入了 AKS *1.13.x*现在，还提供的支持*1.12.a* + *1.12.b*， *1.11.c*  + *1.11 d*， *1.10.e* + *1.10f* （其中，带字母的修补程序版本为两个最新稳定版本）。

引入新的次要版本后，将停止对最早次要版本和修补程序版本的支持。 在发布新的次要版本和即将发生版本停用的 30 天前，会通过 [Azure 更新通道][azure-update-channel]发布公告。 在上面的位置的示例*1.13.x*已发布，已停用的版本是*1.9.g* + *1.9.h*。

在门户中或使用 Azure CLI 部署 AKS 群集时，群集始终会设置为 n-1 次要版本和最新修补程序。 例如，如果支持 AKS *1.13.x*， *1.12.a* + *1.12.b*， *1.11.c*  +  *1.11 d*， *1.10.e* + *1.10f*，创建新的群集的默认版本是*1.11.b*。

## <a name="list-currently-supported-versions"></a>列出当前支持的版本

若要了解你的订阅和区域当前可用的版本，请使用 [az aks get-versions][az-aks-get-versions] 命令。 以下示例列出了 *EastUS* 区域可用的 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

输出结果类似于下面的示例，其中显示了该 Kubernetes 版本*1.13.5*是最新版本可用：

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>常见问题解答

客户升级的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

如果当前为 n-4 版本，则会退出 SLO。 如果从 n-4 版本升级到 n-3 成功，则会返回 SLO。 例如:

- 如果受支持的 AKS 版本*1.13.x*， *1.12.a* + *1.12.b*， *1.11.c*  +  *1.11 d*，和*1.10.e* + *1.10f*和使用不*1.9.g*或*1.9.h*，不 SLO。
- 如果从升级*1.9.g*或*1.9.h*到*1.10.e* 或*1.10.f*成功，你已返回 SLO。

不支持升级到早于 n-4 的版本。 在这种情况下，我们会建议客户创建新的 AKS 群集并重新部署其工作负载。

客户缩放的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

对于 AKS 不支持的次要版本，会继续正确无误地进行扩大或缩小。

客户可以永久保留 Kubernetes 版本吗？

是的。 但是，如果群集不是 AKS 支持的版本之一，群集会退出 AKS SLO。 Azure 不会自动升级群集或将其删除。

如果代理群集不是 AKS 支持的版本之一，主群集会支持哪个版本？

主群集会自动更新到支持的最新版本。

## <a name="next-steps"></a>后续步骤

有关如何升级群集的信息，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
