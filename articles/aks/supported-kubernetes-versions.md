---
title: Azure Kubernetes 服务中支持的 Kubernetes 版本
description: 了解 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 版本支持策略和群集生命周期
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: a4726444e2103228cd26be3d3a6d516b31e315ee
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304327"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中支持的 Kubernetes 版本

Kubernetes 社区大约会每隔三个月发布次要版本。 这些版本包括新增功能和改进。 修补程序版本更为频繁（有时会每周发布），并且仅用于次要版本中的关键 Bug 修复。 这些修补程序版本包括针对影响大量客户以及在生产中基于 Kubernetes 运行的产品的安全漏洞或重大 Bug 的修复。

AKS 旨在验证和发布上游版本30天内的新 Kubernetes 版本, 但要服从版本的稳定性。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用标准[语义版本](https://semver.org/)控制方案。 这意味着 Kubernetes 的每个版本都遵循此编号方案:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

版本中的每个数字表示与以前版本的一般兼容性:

* 当不兼容的 API 更改或向后兼容性可能会损坏时, 主要版本会发生变化。
* 如果更改的功能与其他次要版本向后兼容, 则次要版本会发生更改。
* 当进行向后兼容的 bug 修复时, 修补程序版本会发生更改。

通常情况下, 用户应尽力运行正在运行的次要版本的最新修补程序版本, 例如, 如果你的生产群集在*1.13.6*上, 而*1.13.7*是适用于*1.13*系列的最新可用修补程序版本,一旦能够确保群集充分修补并受支持, 应立即升级到*1.13.7* 。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支持策略

AKS 支持以下四个 Kubernetes 次要版本：

* 在 AKS 中发布的当前次要版本 (N)
* 以前的三个次要版本。 每个受支持的次要版本还支持两个稳定的修补程序。

这称为 "N-3"-(N (最新版本)-3 (次版本))。

例如, 如果 AKS 现在引入了*1.13. x* , 则会为以下版本提供支持:

新次要版本    |    支持的版本列表
-----------------    |    ----------------------
1.13. x               |    1.12. a, 1.12. b, 1.11, 1.11, 1.11, 1.10, 1.10, 1.10, 1.10

其中, "x" 和 ". a" 和 ". b" 为代表性修补程序版本。

有关版本更改和预期的通信的详细信息, 请参阅下面的 "通信"。

引入新的次要版本时, 将弃用并删除支持的最早次版本和修补程序版本。 例如, 如果当前支持的版本列表为:

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

和 AKS 发布 1.13. x, 这意味着 1.9. x 版本 (所有1.9 版本) 将被删除并不支持。

> [!NOTE]
> 请注意, 如果客户运行不受支持的 Kubernetes 版本, 则在请求支持群集时, 系统将要求他们升级。 运行不受支持的 Kubernetes 版本的群集不包含在[AKS 支持策略](https://docs.microsoft.com/azure/aks/support-policies)中。


除了上述次要版本以外, AKS 还支持给定次要版本的两个最新*修补程序** 版本。 例如, 给定以下受支持的版本:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

如果上游 Kubernetes 发布的1.12.3 和1.11.6 和 AKS 释放这些修补程序版本, 则不推荐使用并删除最早的修补程序版本, 并且支持的版本列表将变为:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> 客户不应将群集创建、CI 或其他自动作业固定到特定修补程序版本。 

### <a name="communications"></a>通信

* 适用于 Kubernetes 的新**次要**版本
  * 所有用户都将被公开通知新版本, 并将删除哪个版本。
  * 发布新的修补程序版本时, 会同时删除最旧的修补程序版本。
  * 客户从公共通知日期**60 天**, 升级到受支持的次要版本。
* 对于新的**修补程序**版本 Kubernetes
  * 所有用户都将收到新修补程序版本的通知, 并将其升级到最新的修补程序版本。
  * 用户有**30 天**的时间可升级到支持的更新版本。 删除最旧的之前, 用户有**30 天**的时间升级到受支持的修补程序版本。

AKS 将 "已发布" 定义为 "正式发行版", 并在所有 SLO/服务质量度量中启用, 并且在所有区域中可用。

> [!NOTE]
> 当次要版本不推荐使用或60已删除时, 客户将收到 Kubernetes 版本版本和弃用功能的通知, 以便升级到受支持的版本。 对于修补版本, 客户将获得30天的时间来升级到受支持的版本。

通过以下方式发送通知:

* [AKS 发行说明](https://aka.ms/aks/releasenotes)
* Azure 门户通知
* [Azure 更新通道][azure-update-channel]

### <a name="policy-exceptions"></a>策略例外

AKS 保留了添加或删除新的/现有版本的权利, 这些新版本已被标识为一个或多个关键生产影响了错误或安全问题, 且未提前通知。

可以跳过特定修补程序版本, 也可以根据 bug 或安全问题的严重性来加速部署。

### <a name="azure-portal-and-cli-default-versions"></a>Azure 门户和 CLI 默认版本

当你在门户中或使用 Azure CLI 部署 AKS 群集时, 群集始终设置为第1次次要版本和最新修补程序。 例如, 如果 AKS 支持*1.13. x*, 1.12. *a* +  + *1.12. b*, *1.11*. a*1.11. b*, *1.10. a* + *1.10 b*, 新群集的默认版本为*1.12. b*.

默认情况下, AKS 默认为 N-1 (latestPatch, 例如 1.12. b), 以便为客户提供已知的稳定和修补版本。

## <a name="list-currently-supported-versions"></a>列出当前支持的版本

若要了解哪些版本当前可用于你的订阅和区域, 请使用[az aks get-版本][az-aks-get-versions]命令。 以下示例列出了 *EastUS* 区域可用的 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

输出类似于以下示例, 该示例显示 Kubernetes 版本*1.13.5*是可用的最新版本:

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

如果你采用的是*n-4*版本, 你将不受支持, 将要求你进行升级。 如果从版本 n-4 升级到 n-1 成功, 你现在可以在支持策略中完成。 例如：

- 如果受支持的 AKS 版本为*1.13. x*、1.12. *a* + *1.12. b*、 *1.11. c* + *1.11 d*和*1.10. e* + *1.10 f*为*1.9*或*1.9*版, 你超出了支持范围。
- 如果从*1.9. g*或*1.9*升级到*1.10. e* 或者, *1.10*成功, 你将回到我们的支持策略中。

不支持升级到早于 n-4  的版本。 在这种情况下，我们会建议客户创建新的 AKS 群集并重新部署其工作负载。

**"不支持" 的含义是什么**

"不受支持" 意味着你正在运行的版本在受支持的版本列表外, 在请求支持时, 系统将要求你将群集升级到受支持的版本。 此外, AKS 不会对支持的版本列表之外的群集进行任何运行时或其他保证。

 客户缩放的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

对于 AKS 不支持的次要版本，会继续正确无误地进行扩大或缩小。

 客户可以永久保留 Kubernetes 版本吗？

是的。 但是, 如果群集不在 AKS 支持的版本之一上, 则群集将不再支持 AKS 支持策略。 Azure 不会自动升级群集或将其删除。

 如果代理群集不是 AKS 支持的版本之一，主群集会支持哪个版本？

主群集会自动更新到支持的最新版本。

## <a name="next-steps"></a>后续步骤

有关如何升级群集的信息, 请参阅[升级 Azure Kubernetes Service (AKS) 群集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
