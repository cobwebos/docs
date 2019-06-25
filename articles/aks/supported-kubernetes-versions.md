---
title: Azure Kubernetes 服务中支持的 Kubernetes 版本
description: 了解 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 版本支持策略和群集生命周期
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: b7910ee6f58c582b824cec834d92a24c0e184bfb
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205279"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中支持的 Kubernetes 版本

Kubernetes 社区大约会每隔三个月发布次要版本。 这些版本包括新增功能和改进。 修补程序版本更为频繁（有时会每周发布），并且仅用于次要版本中的关键 Bug 修复。 这些修补程序版本包括针对影响大量客户以及在生产中基于 Kubernetes 运行的产品的安全漏洞或重大 Bug 的修复。

AKS 旨在认证和上游的版本中，根据发布的稳定性的 30 天内发布新的 Kubernetes 版本。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用标准[语义化版本控制](https://semver.org/)版本控制方案。 这意味着每个版本的 Kubernetes，此编号方案如下所示：

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

每个版本中的数字指示与上一版本的常规兼容性：

* 主版本更改时不兼容的 API 更改或向后兼容性可能被破坏。
* 次要版本更改时的功能进行的更改到其他次要版本向后兼容。
* 修补程序的版本进行更改时向后兼容的 bug 修复。

一般情况下，用户应尽力运行它们运行，例如，如果生产群集位于的次版本的最新修补程序版本*1.13.6*并*1.13.7*是最新的修补程序适用于版本*1.13*系列中，应升级到*1.13.7*就立即可以确保你的群集完全完成修补和支持。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支持策略

AKS 支持以下四个 Kubernetes 次要版本：

* 当前的次要版本发布中 AKS (N)
* 以前的三个次要版本。 每个受支持的次要版本还支持两个稳定的修补程序。

这称为"N-3"-(N （最新发行版）-3 （次要版本）)。

例如，如果引入了 AKS *1.13.x*今天，提供的以下版本支持：

新的次要版本    |    受支持的版本列表
-----------------    |    ----------------------
1.13.x               |    1.12.a、 1.12.b、 1.11.a、 1.11.b、 1.10.a、 1.10.b

其中"x"和".a"和".b"是具有代表性的修补程序版本。

有关版本的更改和预期的通信的详细信息，请参阅"通信"下。

当引入新的次要版本时，支持的最早次要版本和修补程序版本弃用并删除了。 例如，如果当前受支持版本列表为：

<a name="supported-version-list"></a>受支持的版本列表
----------------------
1.12.a、 1.12.b、 1.11.a、 1.11.b、 1.10.a、 1.10.b、 1.9.a、 1.9.b

和 AKS 释放 1.13.x，这意味着，将删除 1.9.x 版本 （所有 1.9 版本） 和受支持。

> [!NOTE]
> 请注意，，是否客户运行的是不受支持的 Kubernetes 版本，他们将需要时请求对群集的支持的升级。 群集运行不受支持的 Kubernetes 版本不受[AKS 是否支持策略](https://docs.microsoft.com/azure/aks/support-policies)。


除了上述各项上的次要版本，AKS 支持两个最新*修补程序** 给定的次版本的版本。 例如，对于以下受支持的版本：

<a name="supported-version-list"></a>受支持的版本列表
----------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5

如果上游 Kubernetes 发布 1.12.3 和 1.11.6 和 AKS 释放这些修补程序版本、 最早的修补程序版本将不推荐使用和删除，但受支持的版本列表将成为：

<a name="supported-version-list"></a>受支持的版本列表
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*

> [!NOTE]
> 创建群集、 CI 或其他自动化的作业到特定的修补程序版本，不应锁定客户。 

### <a name="communications"></a>通信

* 对新**次要**的 Kubernetes 版本
  * 新版本，将删除哪些版本的可公开通知所有用户。
  * 发布新的修补程序版本后，会同时删除最早的修补程序版本。
  * 客户可以**60 天**从要升级到受支持的次要版本发布的公共通知日期。
* 对新**修补程序**的 Kubernetes 版本
  * 正在发布的新修补程序版本和升级到最新修补程序版本，所有用户会收到都通知。
  * 用户拥有**30 天**升级到更高版本、 受支持的修补程序版本。 用户拥有**30 天**删除最早前升级到受支持的修补程序版本。

AKS 定义""作为发布公开上市后，在所有 SLO 中启用 / 质量的服务度量值并在所有区域中可用。

> [!NOTE]
> 发布的版本和弃用功能，已弃用或移除用户的次要版本时提供 60 天内升级到受支持版本，客户会收到通知的 Kubernetes。 在修补程序版本的情况下的客户提供 30 天内升级到受支持的版本。

通过发送通知：

* [AKS 发行说明](https://aka.ms/aks/releasenotes)
* Azure 门户通知
* [Azure 更新通道][azure-update-channel]

### <a name="policy-exceptions"></a>策略例外情况

AKS 保留权添加或删除已识别到有影响的 bug 或安全问题，而无需提前通知一个或多个关键生产的新的/现有版本。

特定的修补程序版本可能会跳过，或推出加速具体取决于 bug 或安全问题的严重性。

### <a name="azure-portal-and-cli-default-versions"></a>Azure 门户和 CLI 默认版本

在部署 AKS 群集在门户中或使用 Azure CLI 时，群集始终设置为 N-1 次要版本和最新修补程序。 例如，如果支持 AKS *1.13.x*， *1.12.a* + *1.12.b*， *1.11.a*  +  *1.11.b*， *1.10.a* + *1.10b*，新群集的默认版本是*1.12.b*。

AKS 默认为 N-1 (minor.latestPatch，例如 1.12.b)，为客户提供已知，稳定，并且默认情况下修补版本。

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

如果你位于*n-4*不受支持版本，并将要求升级。 如果从 n-4 版本升级到 n-3 成功，现在已在我们的支持策略。 例如：

- 如果受支持的 AKS 版本*1.13.x*， *1.12.a* + *1.12.b*， *1.11.c*  +  *1.11 d*，和*1.10.e* + *1.10f*和使用不*1.9.g*或*1.9.h*，不受支持。
- 如果从升级*1.9.g*或*1.9.h*到*1.10.e* 或*1.10.f*成功，你已返回内我们支持的策略。

不支持升级到早于 n-4  的版本。 在这种情况下，我们会建议客户创建新的 AKS 群集并重新部署其工作负载。

**外出时的支持是什么意思**

外部支持意味着正在运行的版本不受支持的版本列表中，在您将需要请求支持时，将群集升级到支持的版本。 此外，AKS 不进行任何运行时或在受支持的版本列表之外的群集的其他保证。

 客户缩放的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

对于 AKS 不支持的次要版本，会继续正确无误地进行扩大或缩小。

 客户可以永久保留 Kubernetes 版本吗？

是的。 但是，如果群集不是一个 AKS 支持的版本上，群集不足 AKS 支持策略。 Azure 不会自动升级群集或将其删除。

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
