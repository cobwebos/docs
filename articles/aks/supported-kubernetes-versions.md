---
title: Azure Kubernetes 服务中支持的 Kubernetes 版本
description: 了解 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 版本支持策略和群集生命周期
services: container-service
ms.topic: article
ms.date: 07/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 273c41a523de8b2776982e5229c5a8b618b82c19
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475186"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中支持的 Kubernetes 版本

Kubernetes 社区大约会每隔三个月发布次要版本。 这些版本包括新增功能和改进。 修补程序版本更为频繁（有时会每周发布），并且仅用于次要版本中的关键 Bug 修复。 这些修补程序版本包括针对安全漏洞或主要 bug 的修补程序。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用标准[语义版本](https://semver.org/)控制方案，这意味着每个版本的 Kubernetes 都遵循此编号方案：

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

版本中的每个编写表示与前一版本的一般兼容性：

* 如果发生不兼容的 API 更改或者后向兼容性受到破坏，主要版本将会更改。
* 如果所做的功能更改与其他次要版本后向兼容，则次要版本将会更改。
* 做出后向兼容的 Bug 修复时，修补程序版本将会更改。

用户应瞄准正在运行的次要版本的修补程序的最新版本（例如，如果你的生产群集处于开启状态， **`1.17.7`** 并且 **`1.17.8`** 是适用于*1.17*系列的最新可用修补程序版本），则应尽快升级到 **`1.17.8`** ，以确保群集完全修补并受支持。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支持策略

AKS 定义了一个正式发布的版本，作为在所有 SLO 或 SLA 度量中启用的版本以及在所有区域中可用的版本。 AKS 支持三个 GA 次要版本的 Kubernetes：

* AKS 中发布的最新 GA 次版本（我们将引用为 N）。 
* 以前的两个次要版本。 
* 每个受支持的次要版本还支持最多两（2）个稳定的修补程序。
* AKS 可能还支持预览版本，这些版本已明确标记并受[预览版条款和条件的约束][preview-terms]。

> [!NOTE]
> AKS 使用涉及逐步部署的安全部署做法。 这意味着，新版本可能需要10个工作日内，或在所有区域中都有新版本可用。

AKS 上支持的 Kubernetes 版本窗口称为 "N-2"：（N （最新版本）-2 （次版本））。

例如，如果 AKS 引入*1.17* ，则为以下版本提供支持：

新的次要版本    |    支持的版本列表
-----------------    |    ----------------------
1.17               |    1.17、1.17、1.16、1.16、1.15、1.15、。

其中，“.字母”代表修补程序版本。

引入新的次要版本后，支持的最早次要版本和修补程序版本将被弃用并删除。 例如，如果当前支持的版本列表为：

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

和 AKS 版本1.18。 \* 这意味着所有 1.15 \* 版本都将被删除，并且将在30天内不受支持。

> [!NOTE]
> 请注意，如果客户运行不受支持的 Kubernetes 版本，则在请求群集支持时，系统将要求他们升级。 运行不受支持 Kubernetes 版本的群集未涵盖在 [AKS 支持策略](./support-policies.md)中。

除了上述 AKS，最多支持两个给定次要版本的**修补程序**版本。 因此，请考虑以下受支持的版本：

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

如果 AKS 版本 `1.17.9` 和 `1.16.11` ，则不推荐使用并删除最早的修补程序版本，并且支持的版本列表将变为：

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>支持的 `kubectl` 版本

您可以使用 `kubectl` 相对于*kube-apiserver*版本的较早或较新的次要版本，这与 kubectl 的[Kubernetes 支持策略](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl)一致。

例如，如果你的*kube-apiserver*为*1.17*，则可以使用版本*1.16*来*1.18* `kubectl` 该*kube-apiserver*。

若要安装或更新的版本 `kubectl` ，请运行 `az aks install-cli` 。

## <a name="release-and-deprecation-process"></a>发布和弃用过程

可以在[AKS Kubernetes Release Calendar](#aks-kubernetes-release-calendar)上引用即将发布的版本和弃用功能。

对于 Kubernetes 的新**次要**版本
1. AKS 发布预发布版本，其中包含新版本发布的计划日期，并在删除前至少30天在[AKS 发行说明](https://aka.ms/aks/releasenotes)中发布相应的旧版本弃用。
2. AKS 发布可供所有用户使用 AKS 和门户访问的[服务运行状况通知](../service-health/service-health-overview.md)，并向订阅管理员发送一封电子邮件，其中包含计划的版本删除日期。
3. 用户需要从版本删除**30 天**，才能升级到受支持的次要版本以继续获得支持。

对于 Kubernetes 的新**修补程序**版本
  * 由于修补程序版本的紧急特性，可以在服务可用时将其引入到服务中。
  * 一般情况下，AKS 不会执行广泛的通信来发布新的修补程序版本。 但是，AKS 持续监视和验证可用的 CVE 修补程序，以及时在 AKS 中支持这些修补程序。 如果找到关键修补程序或需要用户操作，AKS 将通知用户升级到新可用的修补程序。
  * 从 AKS 中删除补丁版本后，用户有**30 天**的时间，可以升级到受支持的修补程序并继续接收支持。

### <a name="supported-versions-policy-exceptions"></a>支持的版本策略例外

AKS 有权在不事先发出通行的情况下添加新的版本，或者删除已确定存在一个或多个影响生产的严重 Bug 或安全问题的现有版本。

特定的修补程序版本可能会跳过发布或者加速推出，具体取决于 Bug 或安全问题的严重性。

## <a name="azure-portal-and-cli-versions"></a>Azure 门户和 CLI 版本

在门户中或使用 Azure CLI 部署 AKS 群集时，群集默认为 N-1 次要版本和最新修补程序。 例如，如果 AKS 支持*1.17*、 *1.17*、 *1.16*、 *1.16* *、1.15 和* *1.15*，则所选的默认版本为 " *1.16*"。

若要了解你的订阅和区域当前可用的版本，请使用 [az aks get-versions][az-aks-get-versions] 命令。 以下示例列出了 *EastUS* 区域可用的 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes Release Calendar

对于过去的版本历史记录，请参阅[此处](https://en.wikipedia.org/wiki/Kubernetes#History)。

|  K8s 版本 | 上游版本  | AKS 预览  | AKS GA  | 生命周期终止 |
|--------------|-------------------|--------------|---------|-------------|
| 1.17  | 09-19 年12月  | Jan 19   | 7月20日  | 1.20 GA | 
| 1.18  | 三月-23-20  | 5月20   | 8月20  | 1.21 GA | 
| 1.19  | 8月04-20  | 8月20   | 11 月 20 日  | 1.22 GA | 
| 1.20  | * 11 月20    | * Dec 20   | * Jan 21  | 1.23 GA | 

\*挂起的上游发布日期确认。

## <a name="faq"></a>FAQ

**如果用户使用不受支持的次要版本升级 Kubernetes 群集，会发生什么情况？**

如果你处于*n-3*版本或更低版本，则表示你超出了支持范围，并将要求你进行升级。 从版本 n-1 升级到 n-2 成功后，你将返回到我们的支持策略中。 例如：

- 如果最早受支持的 AKS 版本为*1.15* ，并且你在*1.14*或更早版本上，则你不受支持。
- 从*1.14*升级到1.15 或更高版本时，会返回到我们*的*支持策略。

不支持降级。

**“不在支持范围内”是什么意思**

"不受支持" 意味着你正在运行的版本在受支持的版本列表外，请求支持时，系统将要求你将群集升级到受支持的版本，除非你在版本弃用后处于30天的宽限期。 此外，AKS 不会对支持的版本列表之外的群集进行任何运行时或其他保证。

**如果用户使用不受支持的次要版本缩放 Kubernetes 群集，会发生什么情况？**

对于 AKS 不支持的次要版本，放大或缩小应该会继续工作，但没有服务质量保证，因此强烈建议升级以使群集返回支持。

**用户是否可以永久地停留在 Kubernetes 版本上？**

如果某个群集超出了三（3）次版本的支持，并且发现有安全风险，则 Azure 会联系你以主动升级你的群集。 如果你不执行进一步的操作，Azure 将保留你代表你自动升级群集的权限。

**如果节点池不在某个受支持的 AKS 版本中，控制平面支持哪个版本？**

控制平面必须位于所有节点池的版本窗口中。 有关升级控制平面或节点池的详细信息，请访问有关[升级节点池](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)的文档。

**在群集升级期间，是否可以跳过多个 AKS 版本？**

升级受支持的 AKS 群集时，无法跳过 Kubernetes 次版本。 例如，允许从 1.12.x 升级到 1.13.x，或者从 1.13.x 升级到 1.14.x，但不允许从 1.12.x 升级到 1.14.x。

若要从 1.12.x 升级到 1.14.x，请先从 1.12.x 升级到 1.13.x，然后再从 1.13.x 升级到 1.14.x。

仅当从不受支持的版本升级到受支持的版本时，才能跳过多个版本。 例如，从不受支持的*1.10. x*升级 > 支持的*1.15。*

## <a name="next-steps"></a>后续步骤

有关如何升级群集的信息，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
