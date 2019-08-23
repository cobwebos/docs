---
title: Azure Kubernetes 服务中支持的 Kubernetes 版本
description: 了解 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 版本支持策略和群集生命周期
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 0f2d3079de43767e61dfc1c8d77244da19f13a40
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898864"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中支持的 Kubernetes 版本

Kubernetes 社区大约会每隔三个月发布次要版本。 这些版本包括新增功能和改进。 修补程序版本更为频繁（有时会每周发布），并且仅用于次要版本中的关键 Bug 修复。 这些修补程序版本包括针对影响大量客户以及在生产中基于 Kubernetes 运行的产品的安全漏洞或重大 Bug 的修复。

AKS 旨在完成上游版本发布后的 30 天内，根据该版本的稳定性认证和发布新的 Kubernetes 版本。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用标准的[语义化版本控制](https://semver.org/)方案。 这意味着，Kubernetes 的每个版本都遵循此编号方案：

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

版本中的每个编写表示与前一版本的一般兼容性：

* 如果发生不兼容的 API 更改或者后向兼容性受到破坏，主要版本将会更改。
* 如果所做的功能更改与其他次要版本后向兼容，则次要版本将会更改。
* 做出后向兼容的 Bug 修复时，修补程序版本将会更改。

一般情况下，用户应尽量运行次要版本的最新修补程序版本，例如，如果你的生产群集使用 *1.13.6*，而 *1.13* 系列可用的最新修补程序版本为 *1.13.7*，则你应该尽快升级到 *1.13.7*，以确保群集得到全面的修补并受支持。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支持策略

AKS 支持以下四个 Kubernetes 次要版本：

* AKS 中发布的当前次要版本 (N)
* 以前的三个次要版本。 每个受支持的次要版本还支持两个稳定的修补程序。

这称为“N-3”-（N（最新版本）- 3（次要版本））。

例如，如果 AKS 当前引入了 *1.13.x*，则提供以下版本的支持：

新的次要版本    |    支持的版本列表
-----------------    |    ----------------------
1.13.x               |    1.12.a、1.12.b、1.11.a、1.11.b、1.10.a、1.10.b

其中，“x”、“.a”和“.b”是代表性的修补程序版本。

有关如何传达版本更改和预期后果的详细信息，请参阅下面的“信息传达”。

引入新的次要版本后，支持的最早次要版本和修补程序版本将被弃用并删除。 例如，如果当前支持的版本列表为：

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

而 AKS 发布了 1.13.x，则意味着，1.9.x 版本（所有 1.9 版本）将被删除并停止支持。

> [!NOTE]
> 请注意，如果客户运行不受支持的 Kubernetes 版本，则在请求群集支持时，系统将要求他们升级。 运行不受支持 Kubernetes 版本的群集未涵盖在 [AKS 支持策略](https://docs.microsoft.com/azure/aks/support-policies)中。


除了上述次要版本以外，AKS 还支持给定次要版本的两个最新修补程序* 版本。 例如，假设支持的版本如下：

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

如果上游 Kubernetes 发布了 1.12.3 和 1.11.6，而 AKS 发布了这些修补程序版本，则最早的修补程序版本将被弃用并删除，支持的版本列表现为：

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> 客户不应将群集创建、CI 或其他自动化作业限定为特定的修补程序版本。 

### <a name="communications"></a>信息传达

* 对于 Kubernetes 的新**次要**版本
  * 在发布新版本以及删除版本之前，我们会公开通知所有用户。
  * 在发布新修补程序版本的同时，会删除最早的修补程序版本。
  * 客户可以在公告日期起的 **60 天**内升级到支持的次要版本。
* 对于 Kubernetes 的新**修补程序**版本
  * 在发布新的修补程序版本以及升级到最新修补程序版本之前，我们会通知所有用户。
  * 用户可以在 **30 天**内升级到支持的更新版本。 在删除最早的版本之前，用户可以在 **30 天**内升级到支持的修补程序版本。

AKS 将“发布”版本定义为正式版本，所有 SLO/服务质量衡量方案都采用此术语，并且在所有区域都是如此。

> [!NOTE]
> 在发布和弃用 Kubernetes 版本之前，我们会通知客户；如果弃用/删除了次要版本，用户可在 60 天内升级到支持的版本。 如果发布了修补程序版本，客户可在 30 天内升级到支持的版本。

通过以下方式发送通知：

* [AKS 发行说明](https://aka.ms/aks/releasenotes)
* Azure 门户通知
* [Azure 更新渠道][azure-update-channel]

### <a name="policy-exceptions"></a>策略例外情况

AKS 有权在不事先发出通行的情况下添加新的版本，或者删除已确定存在一个或多个影响生产的严重 Bug 或安全问题的现有版本。

特定的修补程序版本可能会跳过发布或者加速推出，具体取决于 Bug 或安全问题的严重性。

### <a name="azure-portal-and-cli-default-versions"></a>Azure 门户和 CLI 默认版本

在门户中或使用 Azure CLI 部署 AKS 群集时，群集始终会设置为 N-1 次要版本和最新修补程序。 例如，如果 AKS 支持 *1.13.x*、*1.12.a* + *1.12.b*、*1.11.a* + *1.11.b*、*1.10.a* + *1.10b*，则新群集的默认版本为 *1.12.b*。

AKS 默认使用 N-1（“次要版本.最新修补程序版本”，例如 1.12.b），为客户提供已知的稳定修补版本。

## <a name="list-currently-supported-versions"></a>列出当前支持的版本

若要了解你的订阅和区域当前可用的版本，请使用 [az aks get-versions][az-aks-get-versions] 命令。 以下示例列出了 *EastUS* 区域可用的 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

输出类似于以下示例, 该示例显示 Kubernetes 版本*1.14.6*是可用的最新版本:

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

## <a name="faq"></a>常见问题

客户升级的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

如果你当前使用的是 *n-4* 版本，则你不再可接收支持，系统会要求你升级。 如果从版本 n-4 成功升级到了 n-3，则你现在涵盖在我们的支持策略中。 例如：

- 如果支持的 AKS 版本为 *1.13.x*、*1.12.a* + *1.12.b*、*1.11.c* + *1.11d* 和 *1.10.e* + *1.10f*，而你当前使用的是 *1.9.g* 或 *1.9.h*，则你不再可接收支持。
- 如果从 *1.9.g* 或 *1.9.h* 成功升级到了 *1.10.e* 或 *1.10.f*，则你再次涵盖在我们的支持策略中。

不支持升级到早于 n-4 的版本。 在这种情况下，我们会建议客户创建新的 AKS 群集并重新部署其工作负载。

**“停止支持”是什么意思**

“停止支持”是指运行的版本不在支持的版本列表内，当你请求支持时，系统会要求你将群集升级到支持的版本。 此外，对于受支持版本列表以外的群集，AKS 不会在正常运行时间或其他方面做出任何保证。

客户缩放的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？

对于 AKS 不支持的次要版本，会继续正确无误地进行扩大或缩小。

客户可以永久保留 Kubernetes 版本吗？

是的。 但是，如果群集不使用 AKS 支持的版本之一，该群集不会涵盖在 AKS 支持策略内。 Azure 不会自动升级群集或将其删除。

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
