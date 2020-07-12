---
title: Azure Kubernetes 服务 (AKS) 的支持策略
description: 了解 Azure Kubernetes 服务 (AKS) 支持策略、共担责任，以及预览版（或 alpha 或 beta 版）中的功能。
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: ce014fc338ebfad18b91ba65b614ca0a380a3f7c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243845"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes 服务的支持策略

本文详述了 Azure Kubernetes 服务 (AKS) 的技术支持策略和限制。 本文还详细介绍了工作器节点管理、托管控制平面组件、第三方开源组件以及安全或修补程序管理。

## <a name="service-updates-and-releases"></a>服务更新和发行

* 有关发行信息，请参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)。
* 有关预览版功能的信息，请参阅 [AKS 预览版功能和相关项目](https://github.com/Azure/AKS/blob/master/previews.md)。

## <a name="managed-features-in-aks"></a>AKS 中的托管功能

基础设施即服务 (IaaS) 云组件（例如计算或网络组件）允许用户访问低级别控件和自定义选项。 与此相反，AKS 提供了一个全包式 Kubernetes 部署，为客户提供其所需的一组常用配置和功能。 AKS 客户具有有限的自定义、部署和其他选项。 这些客户不需要考虑也不需要直接管理 Kubernetes 群集。

使用 AKS，客户可以获得完全托管的控制平面。 该控制平面包含客户执行操作并向最终用户提供 Kubernetes 群集所需的所有组件和服务。 所有 Kubernetes 组件都由 Microsoft 维护和操作。

Microsoft 通过控制窗格管理和监视以下组件：

* Kubelet 或 Kubernetes API 服务器
* Etcd 或兼容的键-值存储，提供服务质量 (QoS)、可伸缩性和运行时
* DNS 服务（例如，kube-dns 或 CoreDNS）
* Kubernetes 代理或网络

AKS 不是完全托管的群集解决方案。 某些组件（例如工作器节点）具有共担责任，在这些组件中，用户必须帮助维护 AKS 群集。 例如，需要用户输入才能应用工作器节点操作系统 (OS) 安全修补程序。

这些服务是托管的，因为 Microsoft 和 AKS 团队负责部署和操作服务，并负责维护服务的可用性和功能。 客户无法更改这些托管组件。 Microsoft 限制了自定义，以确保一致且可缩放的用户体验。 如需一个可以完全地进行自定义的解决方案，请参阅 [AKS 引擎](https://github.com/Azure/aks-engine)。

## <a name="shared-responsibility"></a>共担责任

创建群集时，客户定义 AKS 创建的 Kubernetes 工作器节点。 客户工作负荷在这些节点上执行。 客户拥有并可以查看或修改工作器节点。

由于客户群集节点执行私有代码并存储敏感数据，因此 Microsoft 支持部门只能以受限的方式访问这些节点。 没有直接的客户许可或帮助，Microsoft 支持人员无法登录这些节点、无法在其中执行命令，也无法查看其日志。

由于工作器节点很敏感，因此，Microsoft 要非常小心地限制其后台管理。 在许多情况下，即使 Kubernetes 主节点、etcd 和其他 Microsoft 托管组件失败，你的工作负荷也会继续运行。 粗心大意修改的工作器节点可能会导致数据和工作负荷丢失，并可能导致群集不受支持。

## <a name="aks-support-coverage"></a>AKS 支持范围

Microsoft 为以下各项提供技术支持：

> [!NOTE]
> Microsoft/AKS 采取的任何群集操作都是以内置 Kubernetes 角色 `aks-service` 和内置角色绑定来进行的 `aks-service-rolebinding` 。 此角色允许 AKS 对群集问题进行故障排除，但是不能修改权限，也不能创建角色或角色绑定，或其他高权限操作。 仅在具有实时 (JIT) access 的活动支持票证下启用角色访问。

* 到 Kubernetes 服务提供并支持的所有 Kubernetes 组件（例如 API 服务器）的连接。
* Kubernetes 控制平面服务（例如 Kubernetes 主节点、API 服务器、etcd 和 kube-dns）的管理、运行时间、QoS 和操作。
* Etcd。 支持包括每隔 30 分钟自动、透明地备份所有 etcd 数据，用于灾难规划和群集状态还原。 这些备份不直接提供给客户或用户。 它们可确保数据的可靠性和一致性。 Etcd。 不支持按需回滚或还原作为功能。
* 适用于 Kubernetes 的 Azure 云提供商驱动程序中的任何集成点。 这包括与其他 Azure 服务的集成，这些服务包括负载均衡器、持久卷或网络（Kubernetes 和 Azure CNI），等等。
* 有关控制平面组件（例如 Kubernetes API 服务器、etcd 和 kube-dns）的自定义的问题。
* 有关网络的问题，例如 Azure CNI、kubenet 或其他网络访问和功能问题。 问题可能包括 DNS 解析、数据包丢失、路由，等等。 Microsoft 为各种网络方案提供支持：
  * 群集和关联组件中的 Kubenet（基本）和高级网络 (Azure CNI)
  * 到其他 Azure 服务和应用程序的连接
  * 入口控制器和入口或负载均衡器配置
  * 网络性能和延迟

Microsoft 不为以下各项提供技术支持：

* 有关如何使用 Kubernetes 的问题。 例如，对于如何创建自定义入口控制器、如何使用应用程序工作负荷，或者如何应用第三方的或开源的软件包或工具，Microsoft 支持部门不提供建议。
  > [!NOTE]
  > Microsoft 支持部门可以针对 AKS 群集功能、自定义和优化（例如，Kubernetes 操作问题和过程）提供建议。
* 未作为 Kubernetes 控制平面的一部分提供的、也不是随 AKS 群集一起部署的第三方开源项目。 这些项目可能包括 Istio、Helm、Envoy 或其他项目。
  > [!NOTE]
  > Microsoft 会尽力为 Helm 和 Kured 等第三方开源项目提供支持。 当第三方开源工具与 Kubernetes 集成时，如果出现了特定于 Azure 云提供商或其他 AKS 的 bug，Microsoft 会为来自 Microsoft 文档的示例和应用程序提供支持。
* 第三方闭源软件。 此类软件可能包括安全扫描工具和网络设备或软件。
* 有关多云或多供应商扩建的问题。 例如，Microsoft 不为与运行联合多公有云供应商解决方案相关的问题提供支持。
* [AKS 文档](./index.yml)中列出的网络自定义之外的那些网络自定义。
  > [!NOTE]
  > Microsoft 可以为与网络安全组 (NSG) 相关的问题和 bug 提供支持。 例如，Microsoft 支持部门可以回答有关 NSG 更新失败或者 NSG 或负载均衡器行为异常的问题。

## <a name="aks-support-coverage-for-worker-nodes"></a>针对工作器节点的 AKS 支持范围

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Microsoft 对 AKS 工作器节点的责任

Microsoft 和客户分担对 Kubernetes 工作器节点的责任，其中：

* 基础操作系统映像具有必需的增项（例如监视和网络代理）。
* 工作器节点会自动接收操作系统修补程序。
* 在工作器节点上运行的 Kubernetes 控制平面组件的问题会自动修复。 组件包括以下各项：
  * Kube-proxy
  * 为 Kubernetes 主组件提供通信路径的网络隧道
  * Kubelet
  * Docker 或 Moby 守护程序

> [!NOTE]
> 在工作器节点上，如果控制平面组件不工作，则 AKS 团队可能需要重启个别组件或整个工作器节点。 这些重启操作会自动执行，并为常见问题提供自动修正。 这些重启操作只发生在节点级别，不会在群集上发生，除非进行了紧急维护或发生了中断。

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>客户对 AKS 工作器节点的责任

Microsoft 不会自动重启工作器节点来应用操作系统级修补程序。 尽管操作系统修补程序会被传递到工作器节点，但是将由客户负责重启工作器节点以应用更改。 共享库、固态混合驱动器 (SSHD) 等守护程序以及系统或操作系统级别的其他组件会自动进行修补。

客户负责执行 Kubernetes 升级。 他们可以通过 Azure 控制面板或 Azure CLI 执行升级。 这适用于包含 Kubernetes 的安全或功能改进的更新。

#### <a name="user-customization-of-worker-nodes"></a>工作器节点的用户自定义
> [!NOTE]
> AKS 工作器节点在 Azure 门户中显示为常规 Azure IaaS 资源。 但是这些虚拟机被部署到自定义的 Azure 资源组（前缀为 MC\\*）中。 可以在 AKS 工作器节点基础配置的基础上增强这些节点。 例如，可以使用安全外壳 (SSH) 更改 AKS 工作器节点，就像更改普通虚拟机一样。 但是，你无法更改基础操作系统映像。 任何自定义更改都无法在升级、缩放、更新或重启后保留。 **但是**，在 *AKS API 带外和作用域外*进行更改会导致 AKS 群集变得不受支持。 请避免更改工作器节点，除非 Microsoft 支持部门指示你进行更改。

执行上面阐述的不受支持的操作（例如所有代理节点的带外解除分配）会使群集变得不受支持。 对于不遵循支持准则配置的控制平面，AKS 保留对这些控制平面进行存档的权利，存档时间可以长达 30 天或更长时间。 AKS 维护群集 etcd 元数据的备份，并可以轻松地重新分配群集。 此重新分配可以由任何使群集重获支持的 PUT 操作（例如升级或缩放到活动代理节点）启动。

AKS 代表客户管理工作器节点的生命周期和操作 - **不支持**修改与工作器节点关联的 IaaS 资源。 不支持的操作的一个示例是通过 VMSS 门户或 VMSS API 手动更改 VMSS 上的配置来自定义节点池 VM 规模集。
 
对于工作负荷特定的配置或包，AKS 建议使用 [Kubernetes 守护程序](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)。

使用 Kubernetes 特权 daemonset 和 init 容器，客户可以在群集工作器节点上优化/修改或安装第三方软件。 此类自定义的示例包括添加自定义安全扫描软件或更新 sysctl 设置。

虽然当满足上述要求时，这是建议的路径，但 AKS 工程和支持人员无法协助排查或诊断破坏性/非功能性修改，或那些由于客户部署的 daemonset 而导致节点不可用的修改。

> [!NOTE]
> 作为一项托管服务，AKS 的最终目标是消除客户对修补程序、更新和日志收集的责任，使服务管理更加完善和方便。 随着服务的端到端管理能力的增强，将来的版本可能会省略一些功能（例如，节点重启和自动修补）。

### <a name="security-issues-and-patching"></a>安全问题和修补

如果在 AKS 的一个或多个组件中发现了安全缺陷，则 AKS 团队将修补所有受影响的群集以缓解此问题。 或者，该团队将向用户提供升级指导。

对于受安全缺陷影响的工作器节点，如果有零停机修补程序可用，AKS 团队会应用该修补程序并将所做的更改通知给用户。

当安全修补程序要求工作器节点重启时，Microsoft 会将此要求通知给客户。 客户负责重启或更新以获取群集修补程序。 如果用户不按照 AKS 指导来应用修补程序，则其群集仍然很容易受到安全问题的影响。

### <a name="node-maintenance-and-access"></a>节点维护和访问

工作器节点是一个共担责任，由客户拥有。 因此，客户可以登录到其工作器节点，并进行可能有害的更改，例如内核更新以及安装或删除程序包。

如果客户进行了破坏性更改或导致控制平面组件脱机或变得不正常，则 AKS 会检测到此故障，并自动将工作器节点还原到以前的工作状态。

尽管客户可以登录并更改工作器节点，但不建议这样做，因为更改可能会导致群集不受支持。

## <a name="network-ports-access-and-nsgs"></a>网络端口、访问和 NSG

作为托管服务，AKS 具有特定的网络和连接要求。 这些要求的灵活性低于正常 IaaS 组件的要求。 在 AKS 中，自定义 NSG 规则、阻止特定端口（例如，使用阻止出站端口 443 的防火墙规则）和将 URL 列入允许列表等操作都可能会导致群集不受支持。

> [!NOTE]
> 目前，AKS 不允许完全锁定群集的出口流量。 若要控制可供群集用于出站流量的 URL 和端口的列表，请参阅[限制出口流量](limit-egress-traffic.md)。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不受支持的 alpha 和 beta Kubernetes 功能

AKS 仅支持上游 Kubernetes 项目中的稳定功能。 除非另有说明，否则 AKS 不支持上游 Kubernetes 项目中提供的 alpha 和 beta 版功能。

在两种情况下，alpha 或 beta 版功能可能会在正式发布之前推出：

* 客户已与 AKS 产品、支持或工程团队会面，并被要求尝试这些新功能。
* 已[通过一个功能标志启用](https://github.com/Azure/AKS/blob/master/previews.md)了这些功能。 客户必须明确选择使用这些功能。

## <a name="preview-features-or-feature-flags"></a>预览版功能或功能标志

对于需要进一步测试并收集用户反馈的功能，Microsoft 会发布新的预览版功能或带功能标志的功能。 可以将这些功能视为预发行版功能或 beta 版功能。

预览版功能或带功能标志的功能不适用于生产。 API 和行为的不断变化、bug 修复和其他更改可能会导致群集不稳定和停机。

公共预览版中的功能会获得“尽力”支持，因为这些功能处于预览版阶段，不适用于生产，并且仅在工作时间内由 AKS 技术支持团队提供支持。 有关详细信息，请参阅：

* [Azure 支持常见问题](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> 预览版功能在 Azure 订阅级别生效。 不要在生产订阅上安装预览版功能。 在生产订阅上，预览版功能可能会更改默认的 API 行为，并影响常规操作。

## <a name="upstream-bugs-and-issues"></a>上游 bug 和问题

考虑到上游 Kubernetes 项目的开发速度，bug 的出现是难免的。 其中一些 bug 无法在 AKS 系统中修补或解决， 只能依靠上游项目（例如 Kubernetes、节点或工作器操作系统，以及内核）的更大修补程序来解决。 对于 Microsoft 拥有的组件（例如 Azure 云提供商），AKS 和 Azure 人员致力于在社区中修复上游问题。

如果某个技术支持问题从根本上来说是一个或多个上游 bug 导致的，则 AKS 支持和工程团队会采取以下措施：

* 确定上游 bug 并将其关联到任何支持详细信息，这样就可以解释此问题为何会影响你的群集或工作负荷。 客户会收到指向所需存储库的链接，因此可以观察问题并了解新版本何时提供修复。
* 提供可能的解决方法或缓解措施。 如果可以缓解此问题，则会在 AKS 存储库中存档一个[已知问题](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)。 已知问题存档将解释以下事项：
  * 具体问题，包括指向上游 bug 的链接。
  * 解决方法，并详述如何升级该解决方案或提供另一持久性解决方案。
  * 根据上游发行节奏，指出将提供此问题的修补程序的大致时间线。
