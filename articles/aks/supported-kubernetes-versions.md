---
title: Azure Kubernetes 服务中支持的 Kubernetes 版本
description: 了解 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 版本支持策略和群集生命周期
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: b6dd91dda559f778eaa8f5a17b46a22020dd8373
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484043"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中支持的 Kubernetes 版本

Kubernetes 社区大约每三个月会发布次要版本。 这些版本包括新增功能和改进。 修补程序版本的发布更为频繁（有时会每周发布），并且仅用于次要版本中的关键 Bug 修复。 这些修补程序版本包括针对影响大量客户以及在生产中基于 Kubernetes 运行的产品的安全漏洞或重大 Bug 的修复。

AKS aims to certify and release new Kubernetes versions within 30 days of an upstream release, subject to the stability of the release.

## <a name="kubernetes-versions"></a>Kubernetes versions

Kubernetes uses the standard [Semantic Versioning](https://semver.org/) versioning scheme. This means that each version of Kubernetes follows this numbering scheme:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Each number in the version indicates general compatibility with the previous version:

* Major versions change when incompatible API changes or backwards compatibility may be broken.
* Minor versions change when functionality changes are made that are backwards compatible to the other minor releases.
* Patch versions change when backwards-compatible bug fixes are made.

In general, users should endeavor to run the latest patch release of the minor version they are running, for example if your production cluster is on *1.12.14* and *1.12.15* is the latest available patch version available for the *1.12* series, you should upgrade to *1.12.15* as soon as you are able to ensure your cluster is fully patched and supported.

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支持策略

> [!NOTE]
> Starting December 9th, 2019 AKS will move to supporting latest (N) - 2 versions of Kubernetes. This change is to conform to the upstream window of support for Kubernetes versions and ensure the latest and most secure versions are being used. To learn more read the [announcement here](https://azure.microsoft.com/updates/azure-kubernetes-service-will-be-retiring-support-for-kubernetes-versions-1-11-and-1-12/).

AKS 支持以下四个 Kubernetes 次要版本：

* The current minor version that is released in AKS (N)
* 以前的三个次要版本。 每个受支持的次要版本还支持两个稳定的修补程序。

This is known as "N-3" - (N (Latest release) - 3 (minor versions)).

For example, if AKS introduces *1.13.a* today, support is provided for the following versions:

New minor version    |    Supported Version List
-----------------    |    ----------------------
1.13.a               |    1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

Where ".a" and ".b" are representative patch versions."a" from 1.13.a can be different from 1.12.a. For example, 1.13.9 and 1.12.8.

For details on communications regarding version changes and expectations, see "Communications" below.

When a new minor version is introduced, the oldest minor version and patch releases supported are deprecated and removed. For example if the current supported version list is:

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

And AKS releases 1.13. *, this means that the 1.9.* versions (all 1.9 versions) will be removed and out of support.

> [!NOTE]
> Please note, that if customers are running an unsupported Kubernetes version, they will be asked to upgrade when requesting support for the cluster. Clusters running unsupported Kubernetes releases are not covered by the [AKS support policies](https://docs.microsoft.com/azure/aks/support-policies).


In addition to the above on minor versions, AKS supports the two latest *patch** releases of a given minor version. For example, given the following supported versions:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

If upstream Kubernetes released 1.12.3 and 1.11.6 and AKS releases those patch versions, the oldest patch versions are deprecated and removed, and the supported version list becomes:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> Customers should not pin cluster creation, CI or other automated jobs to specific patch releases. 

### <a name="communications"></a>通信

* For new **minor** versions of Kubernetes
  * All users are notified publicly of the new version and what version will be removed.
  * When a new patch version is released, the oldest patch release is removed at the same time.
  * Customers have **60 days** from the public notification date to upgrade to a supported minor version release.
* For new **patch** versions of Kubernetes
  * All users are notified of the new patch version being released and to upgrade to the latest patch release.
  * Users have **30 days** to upgrade to a newer, supported patch release. Users have **30 days** to upgrade to a supported patch release before the oldest is removed.

AKS defines "released" as general availability, enabled in all SLO / Quality of Service measurements and available in all regions.

> [!NOTE]
> Customers are notified of Kubernetes version releases and deprecations, when a minor version is deprecated/removed users are given 60 days to upgrade to a supported release. In the case of patch releases, customers are given 30 days to upgrade to a supported release.

#### <a name="notification-channels-for-aks-changes"></a>Notification channels for AKS changes

AKS releases a weekly service update which summarizes new Kubernetes versions, service changes, and component updates that have been released on the service on [github](https://github.com/Azure/AKS/releases).

These changes are rolled to all customers as part of regular maintenance that is offered as part of the managed service, some require explicit upgrades while others require no action.

Notifications are also sent via:

* [AKS Release notes](https://aka.ms/aks/releasenotes)
* Azure 门户中的通知
* [Azure update channel][azure-update-channel]

### <a name="policy-exceptions"></a>Policy Exceptions

AKS reserves the right to add or remove new/existing versions that have been identified to have one or more critical production impacting bugs or security issues without advance notice.

Specific patch releases may be skipped, or rollout accelerated depending on the severity of the bug or security issue.

### <a name="azure-portal-and-cli-default-versions"></a>Azure portal and CLI default versions

When you deploy an AKS cluster in the portal or with the Azure CLI, the cluster is always set to the N-1 minor version and latest patch. For example, if AKS supports *1.13.a*, *1.12.a* + *1.12.b*, *1.11.a* + *1.11.b*, *1.10.a* + *1.10b*, the default version for new clusters is *1.12.b*.

AKS defaults to N-1 (minor.latestPatch, eg 1.12.b) to provide customers a known, stable and patched version by default.

## <a name="list-currently-supported-versions"></a>列出当前支持的版本

To find out what versions are currently available for your subscription and region, use the [az aks get-versions][az-aks-get-versions] command. 以下示例列出了 *EastUS* 区域可用的 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

The output is similar to the following example, which shows that Kubernetes version *1.14.6* is the most recent version available:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.14.6               None available
1.14.5               1.14.6
1.13.10              1.14.5, 1.14.6
1.13.9               1.13.10, 1.14.5, 1.14.6
1.12.8               1.13.9, 1.13.10
1.12.7               1.12.8, 1.13.9, 1.13.10
1.11.10              1.12.7, 1.12.8
1.11.9               1.11.10, 1.12.7, 1.12.8
1.10.13              1.11.9, 1.11.10
1.10.12              1.10.13, 1.11.9, 1.11.10
```

## <a name="faq"></a>常见问题解答

客户升级的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

If you are on the *n-4* version, you are outside of support and will be asked to upgrade. If your upgrade from version n-4 to n-3 succeeds, you are now within our support policies. 例如：

- If the supported AKS versions are *1.13.a*, *1.12.b* + *1.12.c*, *1.11.d* + *1.11.e*, and *1.10.f* + *1.10.g* and you are on *1.9.h* or *1.9.i*, you are outside of support.
- If the upgrade from *1.9.h* or *1.9.i* to *1.10.f* or *1.10.g* succeeds, you are back in the within our support policies.

不支持升级到早于 n-4 的版本。 在这种情况下，我们会建议客户创建新的 AKS 群集并重新部署其工作负载。

**What does 'Out of Support' mean**

'Outside of Support' means that the version you are running is outside of the supported versions list, and you will be asked to upgrade the cluster to a supported version when requesting support. Additionally, AKS does not make any runtime or other guarantees for clusters outside of the supported versions list.

客户缩放的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

对于 AKS 不支持的次要版本，会继续正确无误地进行扩大或缩小。

客户可以永久保留 Kubernetes 版本吗？

可以。 However, if the cluster is not on one of the versions supported by AKS, the cluster is out of the AKS support policies. Azure 不会自动升级群集或将其删除。

如果代理群集不是 AKS 支持的版本之一，主群集会支持哪个版本？

主群集会自动更新到支持的最新版本。

## <a name="next-steps"></a>后续步骤

For information on how to upgrade your cluster, see [Upgrade an Azure Kubernetes Service (AKS) cluster][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
