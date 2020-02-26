---
title: Azure Kubernetes 服务中支持的 Kubernetes 版本
description: 了解 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 版本支持策略和群集生命周期
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593438"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中支持的 Kubernetes 版本

Kubernetes 社区大约会每隔三个月发布次要版本。 这些版本包括新增功能和改进。 修补程序版本更为频繁（有时会每周发布），并且仅用于次要版本中的关键 Bug 修复。 这些修补程序版本包括针对影响大量客户以及在生产中基于 Kubernetes 运行的产品的安全漏洞或重大 Bug 的修复。

AKS 旨在验证和发布上游版本30天内的新 Kubernetes 版本，但要服从版本的稳定性。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用标准[语义版本](https://semver.org/)控制方案。 这意味着 Kubernetes 的每个版本都遵循此编号方案：

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

版本中的每个数字表示与以前版本的一般兼容性：

* 当不兼容的 API 更改或向后兼容性可能会损坏时，主要版本会发生变化。
* 如果更改的功能与其他次要版本向后兼容，则次要版本会发生更改。
* 当进行向后兼容的 bug 修复时，修补程序版本会发生更改。

用户应瞄准正在运行的次要版本的修补程序的最新版本（例如，如果你的生产群集在*1.12.14*上，而*1.12.15*是*1.12*系列可用的最新可用修补程序版本），则应尽快升级到*1.12.15* ，因为你能够确保群集完全修补并受支持。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支持策略

AKS 支持三种次要版本的 Kubernetes：

* 在 AKS 中发布的当前次要版本（N）
* 两个以前的次要版本。 每个受支持的次要版本还支持两个稳定的修补程序。

这称为 "N-2"：（N （最新版本）-2 （次版本））。

例如，如果 AKS 引入*1.15* ，则为以下版本提供支持：

新次要版本    |    支持的版本列表
-----------------    |    ----------------------
1.15               |    1.15，1.15，1.14，1.14，1.13. e，1.13. f

其中，". 字母" 代表修补程序版本。

有关版本更改和预期的通信的详细信息，请参阅下面的 "通信"。

引入新的次要版本时，将弃用并删除支持的最早次版本和修补程序版本。 例如，如果当前支持的版本列表为：

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

和 AKS 版本1.16。 *，这意味着1.13。* 版本（所有1.13 版本）将被删除，并且不受支持。

> [!NOTE]
> 请注意，如果客户运行不受支持的 Kubernetes 版本，则在请求支持群集时，系统将要求他们升级。 运行不受支持的 Kubernetes 版本的群集不包含在[AKS 支持策略](https://docs.microsoft.com/azure/aks/support-policies)中。

除了上述次要版本以外，AKS 还支持给定次要版本的两个最新**修补程序**版本。 例如，给定以下受支持的版本：

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

如果上游 Kubernetes 发布的1.15.3 和1.14.6 和 AKS 释放这些修补程序版本，则不推荐使用并删除最早的修补程序版本，并且支持的版本列表将变为：

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>通信

* 适用于 Kubernetes 的新**次要**版本
  * 所有用户都将被公开通知新版本，并将删除哪个版本。
  * 发布新的修补程序版本时，会同时删除最旧的修补程序版本。
  * 客户从公共通知日期起**30 天**后，升级到受支持的次要版本。
* 对于新的**修补程序**版本 Kubernetes
  * 所有用户都将收到新修补程序版本的通知，并将其升级到最新的修补程序版本。
  * 删除最早版本之前，用户有**30 天**的时间升级到更新的受支持修补程序版本。

AKS 将 "已发布的版本" 定义为正式发布的版本，在所有 SLO/质量的服务度量中启用并在所有区域中可用。 AKS 可能还支持预览版本，这些版本已明确标记并受预览版条款和条件的约束。

#### <a name="notification-channels-for-aks-changes"></a>AKS 更改的通知通道

AKS 发布常规的服务更新，这些更新汇总了在[GitHub](https://github.com/Azure/AKS/releases)上的服务上发布的新 Kubernetes 版本、服务更改和组件更新。

这些更改将作为日常维护的一部分被提供给所有客户，作为托管服务的一部分，某些客户需要显式升级，而其他人则不需要采取任何措施。

还可以通过以下方式发送通知：

* [AKS 发行说明](https://aka.ms/aks/releasenotes)
* Azure 门户中的通知
* [Azure 更新通道][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>支持的版本策略例外

AKS 保留了添加或删除新的/现有版本的权利，这些新版本已被标识为一个或多个关键生产影响了错误或安全问题，且未提前通知。

可以跳过特定修补程序版本，也可以根据 bug 或安全问题的严重性来加速部署。

### <a name="azure-portal-and-cli-default-versions"></a>Azure 门户和 CLI 默认版本

当你在门户中或使用 Azure CLI 部署 AKS 群集时，群集将默认为 N 1 次版本和最新修补程序。 例如，如果 AKS 支持*1.15*、 *1.15*、 *1.14*、 *1.14*和*1.13. f* *，则*所选的默认版本为*1.14. c*。

默认情况下，AKS 选择 N-1 的默认值，以便为客户提供已知、稳定和修补的版本。

## <a name="list-currently-supported-versions"></a>列出当前支持的版本

若要了解哪些版本当前可用于你的订阅和区域，请使用[az aks get-版本][az-aks-get-versions]命令。 以下示例列出了 *EastUS* 区域可用的 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>常见问题解答

客户升级的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

如果你采用的是*n-1*版，则你将不受支持，将要求你进行升级。 如果从版本 n-1 升级到 n-2 成功，你现在将处于我们的支持策略中。 例如：

- 如果最早受支持的 AKS 版本为*1.13. a* ，并且你在*1.12. b*或更早版本上，则你超出了支持范围。
- 如果从*1.12. b*升级到*1.13。* 或者更高版本成功，则返回到我们的支持策略中。

不支持升级到不支持的*N-2*窗口版本。 在这种情况下，我们建议客户创建新的 AKS 群集，然后在支持的窗口中将其工作负荷部署为版本。

**"不受支持" 的含义是什么**

"不受支持" 意味着你正在运行的版本在受支持的版本列表外，在请求支持时，系统将要求你将群集升级到受支持的版本。 此外，AKS 不会对支持的版本列表之外的群集进行任何运行时或其他保证。

客户缩放的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

对于 AKS 不支持的次要版本，放大或缩小应该会继续工作，但强烈建议升级以使群集返回支持。

客户可以永久保留 Kubernetes 版本吗？

是的。 但是，如果群集不在 AKS 支持的版本之一上，则群集将不再支持 AKS 支持策略。 Azure 不会自动升级群集或将其删除。

**如果节点池不在某个受支持的 AKS 版本中，控制平面支持哪个版本？**

控制平面必须位于所有节点池版本的窗口中。 有关升级控制平面或节点池的详细信息，请访问有关[升级节点池](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)的文档。

## <a name="next-steps"></a>后续步骤

有关如何升级群集的信息，请参阅[升级 Azure Kubernetes Service （AKS）群集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
