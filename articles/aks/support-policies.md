---
title: Azure Kubernetes 服务 (AKS) 的支持策略
description: 了解 Azure Kubernetes 服务 (AKS) 预览版（或者 alpha 或 beta 版）中的支持策略、共担责任和功能。
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 8c1736fcd4356e0778c3b36301b4490764efab76
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240879"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes 服务的支持策略

本文提供有关 Azure Kubernetes 服务 (AKS) 的技术支持策略和限制的详细信息。 本文还详细介绍了工作器节点管理、托管控制平面组件、第三方开源组件，以及安全性或修补程序管理。

## <a name="service-updates-and-releases"></a>服务更新和版本

* 有关详细信息，请参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)。
* 有关预览版功能的信息，请参阅[AKS preview 功能和相关项目](https://github.com/Azure/AKS/blob/master/previews.md)。

## <a name="managed-features-in-aks"></a>AKS 中的托管功能

基本的基础结构即服务 (IaaS) 云组件（例如计算或网络组件）可让用户访问低级别的控件机制和自定义选项。 相比之下，AKS 提供统包式的 Kubernetes 部署，为客户提供一组通用配置和所需的功能。 AKS 客户可用的自定义功能、部署和其他选项有限。 这些客户不需要考虑或直接管理 Kubernetes 群集。

借助 AKS，客户可以获得一个完全托管型的控制平面。 该控制平面包含客户操作以及向最终用户提供 Kubernetes 群集的全部所需组件和服务。 所有 Kubernetes 组件都由 Microsoft 维护和操作。

Microsoft 通过控件窗格管理和监视以下组件：

* Kubelet 或 Kubernetes API 服务器
* Etcd 或兼容的键-值存储，提供服务质量 (QoS)、可伸缩性和运行时
* DNS 服务（例如 kube-dns 或 CoreDNS）
* Kubernetes 代理或网络

AKS 不是完全托管型的群集解决方案。 某些组件（例如工作器节点）实行分担责任制，用户必须帮助维护 AKS 群集。 例如，必须提供用户输入才能应用工作器节点操作系统 (OS) 安全修补程序。

*管理*服务时，MICROSOFT 和 AKS 团队将部署、操作并负责服务可用性和功能。 客户无法改动这些托管组件。 Microsoft 限制了自定义，以确保用户体验的一致性和可缩放性。 有关完全可自定义的解决方案，请参阅 [AKS 引擎](https://github.com/Azure/aks-engine)。

> [!NOTE]
> AKS 工作器节点作为常规的 Azure IaaS 资源显示在 Azure 门户中。 但是，这些虚拟机将部署到自定义的 Azure 资源组（带有 MC\\* 前缀）。 无法更改 AKS 工作器节点。 例如，可以使用安全外壳 (SSH) 更改 AKS 工作器节点，如同更改普通虚拟机一样（但是，无法更改基本的 OS 映像，并且更新或重新启动后，更改不会持久保留），并可以将其他 Azure 资源附加到 AKS 工作器节点。 但是，如果更改带外管理和自定义项，AKS 群集可能变得不受支持。 请避免更改辅助角色节点，除非 Microsoft 支持部门指示你进行更改。

## <a name="shared-responsibility"></a>共担责任

创建群集时，客户需定义 AKS 创建的 Kubernetes 工作器节点。 客户工作负荷将在这些节点上执行。 客户拥有并可以查看或修改工作器节点。

由于客户群集节点执行专用代码并存储敏感数据，因此 Microsoft 支持部门只能以有限的方式访问这些数据。 Microsoft 支持部门不能登录到，不能在中执行命令，也不能查看这些节点的日志，无需快速获得客户权限或协助。

由于辅助角色节点是敏感的，因此，Microsoft 要非常小心地限制其后台管理。 在许多情况下，即使 Kubernetes 主节点、etcd 和其他 Microsoft 托管组件发生故障，工作负荷也将继续运行。 不小心修改工作器节点可能会导致数据和工作负荷丢失，并使群集变得不受支持。

## <a name="aks-support-coverage"></a>AKS 支持范围

Microsoft 为以下各项提供技术支持：

* 连接到 Kubernetes 服务提供和支持的所有 Kubernetes 组件，例如 API 服务器。
* Kubernetes 控制平面服务（例如 Kubernetes 主节点、API 服务器、etcd 和 kube-dns）的管理、运行时间、QoS 和操作。
* Etcd。 支持包括每隔 30 分钟以透明方式自动备份所有 etcd 数据，以实现灾难规划和群集状态还原。 客户或用户不可直接使用这些备份。 这些备份用于确保数据的可靠性和一致性。
* 适用于 Azure 云提供程序驱动程序中的任何集成点。 这包括与负载均衡器、永久性卷或网络组件（Kubernetes 和 Azure CNI）等其他 Azure 服务的集成。
* 有关控制平面组件（例如 Kubernetes API 服务器、etcd 和 kube-dns）等控制平面组件的自定义问题。
* 有关网络组件（例如 Azure CNI、kubenet）的问题，或其他网络访问和功能问题。 问题可能包括 DNS 解析、数据包丢失、路由等。 Microsoft 支持各种网络方案：
  * 群集和关联组件中的 Kubenet（基本）和高级网络 (Azure CNI)
  * 连接到其他 Azure 服务和应用程序
  * 入口控制器以及入口或负载均衡器配置
  * 网络性能和延迟

Microsoft 不为以下各项提供技术支持：

* 有关 Kubernetes 用法的问题。 例如，Microsoft 支持部门不会提供有关如何创建自定义入口控制器、使用应用程序工作负载或应用第三方或开源软件包或工具的建议。
  > [!NOTE]
  > Microsoft 支持部门可以建议 AKS 群集功能、自定义和优化（例如，Kubernetes 操作问题和过程）。
* 不是作为 Kubernetes 控制平面的一部分提供的，或者不是在 AKS 群集中部署的第三方开源项目。 这些项目可能包括 Istio、Helm、Envoy 等等。
  > [!NOTE]
  > Microsoft 可以为第三方开源项目（如 Helm 和 Kured）提供最大努力支持。 当第三方开源工具与 Kubernetes Azure 云提供程序或其他特定于 AKS 的 bug 集成时，Microsoft 支持 Microsoft 文档中的示例和应用程序。
* 第三方闭源软件。 此类软件可能包括安全扫描工具以及网络设备或软件。
* 有关多云或多供应商扩建的问题。 例如，Microsoft 不支持运行联合 multipublic 云供应商解决方案相关的问题。
* [AKS 文档](https://docs.microsoft.com/azure/aks/)中未列出的网络自定义。
  > [!NOTE]
  > Microsoft 支持与网络安全组（Nsg）相关的问题和 bug。 例如，Microsoft 支持部门可以回答有关更新 NSG 失败或意外的 NSG 或负载均衡器行为的问题。

## <a name="aks-support-coverage-for-worker-nodes"></a>针对工作器节点的 AKS 支持范围

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>AKS 工作节点的 Microsoft 责任

Microsoft 和客户分担 Kubernetes 工作节点的责任，其中：

* 基本 OS 映像收到了必需的新增功能（例如监视和网络代理）。
* 工作器节点自动收到了 OS 修补程序。
* 在工作器节点上运行的 Kubernetes 控制平面组件的问题可自动得到解决。 组件包括：
  * Kube 代理
  * 为 Kubernetes 主控组件提供通信路径的网络隧道
  * Kubelet
  * Docker 或 Moby 守护程序

> [!NOTE]
> 在辅助角色节点上，如果控制平面组件不可操作，则 AKS 团队可能需要重新启动单个组件或整个工作节点。 这些重新启动操作会自动执行，并为常见问题提供自动修正。 这些重启仅发生在_节点_级别而不是群集上，除非它们是紧急维护或中断。

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>客户对 AKS 工作器节点承担的责任

Microsoft 不会自动重新启动工作节点以应用操作系统级修补程序。 尽管 OS 修补程序是为工作器节点交付的，但客户需负责重新启动工作器节点来应用更改。 系统或 OS 级别的共享库、守护程序（例如固态混合驱动器 (SSHD)）和其他组件将自动得到修补。

客户负责执行 Kubernetes 升级。 他们可以通过 Azure 控制面板或 Azure CLI 执行升级。 这适用于包含 Kubernetes 安全性或功能改进的更新。

> [!NOTE]
> 由于 AKS 是一个托管服务，其最终目标包括消除修补、更新和日志收集方面的责任，使服务管理变得更完整且无需人工干预。 由于用于端到端管理的服务容量不断增大，将来的版本可能会省略某些功能（例如，节点重新启动和自动修补）。

### <a name="security-issues-and-patching"></a>安全问题和修补

如果在 AKS 的一个或多个组件中找到安全缺陷，AKS 团队将修补所有受影响的群集以缓解此问题。 或者，AKS 团队将为用户提供升级指导。

对于安全缺陷所影响到的工作器节点，如果有不会造成任何停机的修补程序可用，则 AKS 团队会应用该修补程序，并通知用户发生了更改。

当安全修补程序需要重新启动工作节点时，Microsoft 会通知客户此要求。 客户负责重新启动或更新以获取群集修补程序。 如果用户未根据 AKS 的指导应用修补程序，其群集仍会受到安全问题的影响。

### <a name="node-maintenance-and-access"></a>节点维护和访问

工作器节点由客户拥有，其责任由客户与 Azure 共同分担。 因此，客户可能会登录到其工作器节点并进行潜在有害的更改，例如更新内核，以及安装或删除包。

如果客户做出了破坏性的更改或者导致控制平面组件脱机或异常，则 AKS 会检测到这种故障，并将工作器节点自动还原到以前的正常运行状态。

尽管客户可以登录并更改工作器节点，但我们不建议这样做，因为做出更改可能会导致群集不受支持。

## <a name="network-ports-access-and-nsgs"></a>网络端口、访问和 NSG

作为一项托管服务，AKS 在网络和连接方面存在特定的要求。 与普通 IaaS 组件的要求相比，这些要求不太灵活。 在 AKS 中，自定义 NSG 规则、阻止特定端口（例如，使用防火墙规则阻止出站端口 443）以及将 URL 加入允许列表等操作可能导致群集不受支持。

> [!NOTE]
> 目前，AKS 不允许你完全锁定群集的出口流量。 若要控制群集可用于出站流量的 Url 和端口的列表，请参阅[限制传出流量](limit-egress-traffic.md)。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不支持的 alpha 和 beta Kubernetes 功能

AKS 仅支持上游 Kubernetes 项目中的稳定功能。 除非另有说明，否则，AKS 不支持上游 Kubernetes 项目中提供的 alpha 和 beta 功能。

在以下两种情况下，alpha 或 beta 功能可能在正式版发布之前便已推出：

* 客户已与 AKS 产品、支持或工程团队会谈，团队请求他们尝试这些新功能。
* 这些功能已[通过功能标志启用](https://github.com/Azure/AKS/blob/master/previews.md)。 客户必须明确选择使用这些功能。

## <a name="preview-features-or-feature-flags"></a>预览功能或功能标志

对于需要扩展测试和用户反馈的功能，Microsoft 发布了功能标志后的新预览版功能或功能。 请将这些功能视为预发行版或 beta 功能。

预览功能或功能标志功能并非适用于生产。 API 和行为的持续更改、bug 修复及其他更改可能导致群集不稳定和停机。

公共预览版中的功能处于 "最大努力" 的支持下，因为这些功能处于预览阶段，不适用于生产环境，并且仅在营业时间内 AKS 技术支持团队提供支持。 有关其他信息，请参阅：

* [Azure 支持常见问题](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> 预览功能在 Azure*订阅*级别生效。 请勿在生产订阅上安装预览功能。 在生产订阅上，预览功能可以更改默认的 API 行为，并影响常规操作。

## <a name="upstream-bugs-and-issues"></a>上游 bug 和问题

由于上游 Kubernetes 项目的开发速度，不可避免地会出现 bug。 其中的某些 bug 无法在 AKS 系统内部得到修补或解决。 bug 修复需要对上游项目（例如 Kubernetes、节点或辅助角色操作系统以及内核）应用较大的修补程序。 对于 Microsoft 拥有的组件（如 Azure 云提供商），AKS 和 Azure 人员承诺解决社区中的上游问题。

如果判定某个技术支持问题是一个或多个上游 bug 的根本原因，则 AKS 支持和工程团队将会：

* 使用任何支持详细信息来识别并链接上游 bug，以帮助解释为何此问题会影响群集或工作负荷。 客户会收到所需存储库的链接，以便可以观察问题，并了解何时有新的版本可以提供修复措施。
* 提供潜在的解决方法或缓解措施。 如果该问题可以缓解，则会在 AKS 存储库中创建[已知问题](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)的备案。 已知问题备案将会解释：
  * 该问题，包括上游 bug 的链接。
  * 解决方法，以及有关解决方案升级或其他持久化措施的详细信息。
  * 问题包含内容的大致时间线，根据上游版本发布频率提供。
