---
title: Azure Kubernetes 服务中支持的 Kubernetes 版本
description: 了解 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 版本支持策略和群集生命周期
services: container-service
ms.topic: article
ms.date: 07/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 45c60069c27a2bb9b34cc53eb394bd44a04bd0ba
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144611"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中支持的 Kubernetes 版本

Kubernetes 社区大约会每隔三个月发布次要版本。 这些版本包括新增功能和改进。 修补程序版本更为频繁（有时会每周发布），并且仅用于次要版本中的关键 Bug 修复。 这些修补程序版本包括针对安全漏洞或主要 bug 的修复。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用标准[语义化版本控制](https://semver.org/)版本控制方案，这意味着每个版本的 Kubernetes 都遵循此编号方案：

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

用户应尽量运行次要版本的最新修补程序版本，例如，如果你的生产群集使用 `1.17.7`，而 1.17 系列可用的最新修补程序版本为 `1.17.8`，则你应尽快升级到 `1.17.8`，以确保群集得到全面的修补并受支持 。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支持策略

AKS 定义了一个正式发布版，所有 SLO 或 SLA 衡量方案都采用此版本，并且在所有区域提供。 AKS 支持三个 Kubernetes GA 次要版本：

* AKS 中发布的最新 GA 次要版本（我们将其称为 N）。 
* 以前的两个次要版本。 
* 每个受支持的次要版本还支持最多两 (2) 个稳定的修补程序。
* AKS 可能还支持预览版本，这些版本带有明确的标签，使用时需遵守[预览版条款和条件][preview-terms]。

> [!NOTE]
> AKS 使用涉及逐步区域部署的安全部署做法。 这意味着，可能需要最长 10 个工作日才能使新发布或新版本在所有区域中均可用。

AKS 上的 Kubernetes 版本的受支持窗口称为“N-2”：(N (最新版本) -2 (次要版本))。

例如，如果 AKS 当前引入了 1.17.a，则提供以下版本的支持：

新的次要版本    |    支持的版本列表
-----------------    |    ----------------------
1.17.a               |    1.17.a、1.17.b、1.16.c、1.16.d、1.15.e、1.15.f

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

并且 AKS 发布了 1.18.\*，这意味着所有 1.15.\* 版本将在 30 天内被删除且不再受支持。

> [!NOTE]
> 请注意，如果客户运行不受支持的 Kubernetes 版本，则在请求群集支持时，系统将要求他们升级。 运行不受支持 Kubernetes 版本的群集未涵盖在 [AKS 支持策略](./support-policies.md)中。

除上述内容以外，AKS 还支持最多两个给定次要版本的修补程序版本。 因此，假设支持的版本如下：

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

如果 AKS 发布了 `1.17.9` 和 `1.16.11`，则最早的修补程序版本将被弃用并删除，支持的版本列表现为：

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>支持的 `kubectl` 版本

你可以使用一个相对于 kube-apiserver 版本 `kubectl` 较旧或较新的次要版本，这与 [kubectl 的 Kubernetes 支持策略](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl)一致。

例如，如果你的 kube-apiserver 处于 1.17，则可以将版本 1.16 到 1.18 的 `kubectl` 与该 kube-apiserver 结合使用    。

若要安装或更新你版本的 `kubectl`，请运行 `az aks install-cli`。

## <a name="release-and-deprecation-process"></a>发布和弃用流程

你可以在 [AKS Kubernetes 发布日历](#aks-kubernetes-release-calendar)上参考即将推出的版本发布和弃用。

对于 Kubernetes 的新**次要**版本
1. AKS 至少在删除前 30 天在 [AKS 发行说明](https://aka.ms/aks/releasenotes)中发布预告，其中包含新版本发布的计划日期以及相应的旧版本弃用。
2. AKS 会向具有 AKS 和门户访问权限的所有用户发布一个[服务运行状况通知](../service-health/service-health-overview.md)，并向订阅管理员发送一封电子邮件，其中包含计划的版本删除日期。
3. 自版本删除起，用户有 30 天的时间升级到受支持的次要版本发布，以继续获得支持。

对于 Kubernetes 的新**修补程序**版本
  * 由于修补程序版本的紧急性质，可以在修补程序变为可用时将其引入到服务中。
  * 通常情况下，对于新修补程序版本的发布，AKS 不会进行广泛的通信。 但是，AKS 会持续监视和验证可用的 CVE 修补程序，以便及时在 AKS 中支持它们。 如果发现修补程序或需要用户进行操作，AKS 将通知用户升级到新发布的修补程序。
  * 自从 AKS 中删除修补程序版本之时起，用户有 30 天的时间升级到受支持的修补程序并继续获取支持。

### <a name="supported-versions-policy-exceptions"></a>支持的版本策略例外情况

AKS 有权在不事先发出通行的情况下添加新的版本，或者删除已确定存在一个或多个影响生产的严重 Bug 或安全问题的现有版本。

特定的修补程序版本可能会跳过发布或者加速推出，具体取决于 Bug 或安全问题的严重性。

## <a name="azure-portal-and-cli-versions"></a>Azure 门户和 CLI 版本

在门户中或使用 Azure CLI 部署 AKS 群集时，群集默认为 N-1 次要版本和最新修补程序。 例如，如果 AKS 支持 1.17.a、1.17.b、1.16.c、1.16.d、1.15.e 和 1.15.f，则选择的默认版本为 1.16.c      。

若要了解你的订阅和区域当前可用的版本，请使用 [az aks get-versions][az-aks-get-versions] 命令。 以下示例列出了 *EastUS* 区域可用的 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes 发布日历

对于过去的版本历史记录，请参阅 [此处](https://en.wikipedia.org/wiki/Kubernetes#History)。

|  K8s 版本 | 上游版本  | AKS 预览版  | AKS GA  | 生命周期终止 |
|--------------|-------------------|--------------|---------|-------------|
| 1.17  | 19-12-09  | 2019 年 1 月   | 7月2020  | 1.20 GA | 
| 1.18  | 20-03-23  | 2020 年 5 月   | 2020年8月  | 1.21 GA | 
| 1.19  | 20-08-04  | 2020年8月   | 11月2020  | 1.22 GA | 
| 1.20  | * 11 月2020    | * Dec 2020   | * Jan 2021  | 1.23 GA | 

\* 待定的上游发布日期确认。

## <a name="faq"></a>常见问题解答

**用户升级的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？**

如果你当前使用的是 n-3 或更低版本，则意味着你已不在支持范围，系统会要求你升级。 从版本 n-3 成功升级到 n-2 后，你将重新涵盖在我们的支持策略中。 例如：

- 如果支持的最低 AKS 版本为 1.15.a，而你使用的版本是 1.14.b 或更低，则你无法获得支持 。
- 从 1.14.b 成功升级到 1.15.a 或更高版本之后，你将重新涵盖在我们的支持策略中 。

不支持降级。

**“不在支持范围内”是什么意思**

“停止支持”是指运行的版本不在支持的版本列表内，当你请求支持时，系统会要求你将群集升级到支持的版本，除非你正处于版本弃用后的 30 天宽限期内。 此外，对于受支持版本列表以外的群集，AKS 不会在正常运行时间或其他方面做出任何保证。

**用户缩放的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？**

对于 AKS 不支持的次要版本，横向缩减或扩展可继续进行，但这没有服务质量保证，因此我们强烈建议升级，使群集重新获取支持。

**用户可以永久保留 Kubernetes 版本吗？**

如果群集有三 (3) 个以上的次要版本不受支持，并被发现存在安全风险，则 Azure 会联系你主动升级群集。 如果你不采取进一步的措施，Azure 将保留代表你自动升级群集的权利。

**如果节点池不在某个受支持的 AKS 版本中，控制平面支持哪个版本？**

控制平面必须位于所有节点池的版本窗口中。 有关升级控制平面或节点池的详细信息，请访问有关[升级节点池](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)的文档。

**在群集升级期间，是否可以跳过多个 AKS 版本？**

升级受支持的 AKS 群集时，不能跳过 Kubernetes 次要版本。 例如，允许从 1.12.x 升级到 1.13.x，或者从 1.13.x 升级到 1.14.x，但不允许从 1.12.x 升级到 1.14.x。

若要从 1.12.x 升级到 1.14.x，请先从 1.12.x 升级到 1.13.x，然后再从 1.13.x 升级到 1.14.x。

仅当从不受支持的版本升级回受支持的版本时，才可以跳过多个版本。 例如，可以完成从不受支持的 1.10.x 升级到受支持的 1.15.x 。

## <a name="next-steps"></a>后续步骤

有关如何升级群集的信息，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
