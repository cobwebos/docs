---
title: Azure Kubernetes Service （AKS）的支持策略
description: 了解 Azure Kubernetes Service （AKS）支持策略、共享责任和预览版（或 alpha 或 beta 版）中的功能。
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593574"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes 服务的支持策略

本文提供有关 Azure Kubernetes 服务（AKS）的技术支持策略和限制的详细信息。 本文还详细介绍了工作节点管理、托管控制平面组件、第三方开源组件以及安全性或修补程序管理。

## <a name="service-updates-and-releases"></a>服务更新和发布

* 有关版本信息，请参阅[AKS 发行说明](https://github.com/Azure/AKS/releases)。
* 有关预览版功能的信息，请参阅[AKS preview 功能和相关项目](https://github.com/Azure/AKS/blob/master/previews.md)。

## <a name="managed-features-in-aks"></a>AKS 中的托管功能

基础结构即服务（IaaS）云组件，如计算或网络组件，使用户能够访问低级别控件和自定义选项。 与此相反，AKS 提供了一个全包式 Kubernetes 部署，为客户提供其所需的一组常用配置和功能。 AKS 客户的自定义、部署和其他选项是有限的。 这些客户无需担心或直接管理 Kubernetes 群集。

使用 AKS，客户获取完全托管的*控制平面*。 控制平面包含客户运行所需的所有组件和服务，并向最终用户提供 Kubernetes 群集。 所有 Kubernetes 组件都由 Microsoft 维护和操作。

Microsoft 通过控件窗格管理和监视以下组件：

* Kubelet 或 Kubernetes API 服务器
* Etcd 或兼容的键-值存储，提供服务质量（QoS）、可伸缩性和运行时
* DNS 服务（例如，kube 或 CoreDNS）
* Kubernetes 代理或网络

AKS 不是完全托管的群集解决方案。 某些组件（例如辅助角色节点）具有*共同的责任*，用户必须在该位置帮助维护 AKS 群集。 例如，需要用户输入才能应用辅助节点操作系统（OS）安全修补程序。

*管理*服务时，MICROSOFT 和 AKS 团队将部署、操作并负责服务可用性和功能。 客户无法更改这些托管组件。 Microsoft 限制了自定义，以确保用户体验的一致性和可缩放性。 有关完全可自定义的解决方案，请参阅[AKS Engine](https://github.com/Azure/aks-engine)。

> [!NOTE]
> AKS 工作节点在 Azure 门户中显示为常规的 Azure IaaS 资源。 但这些虚拟机部署到自定义 Azure 资源组（使用 MC\\*）作为前缀。 可以更改 AKS 辅助角色节点。 例如，你可以使用安全外壳（SSH）更改常规虚拟机（但不能更改基础操作系统映像，并且更改可能不会通过更新或重新启动）中的 AKS 辅助角色节点，还可以将其他 Azure 资源附加到 AKS辅助角色节点。 但在进行*带外管理和自定义*的更改时，AKS 群集可能会似乎。 请避免更改辅助角色节点，除非 Microsoft 支持部门指示你进行更改。

发出前面定义的不受支持的操作，如所有代理节点的带外释放，将导致不受支持的群集。 AKS 保留了存档控制平面的权利，该控制面已配置为延长时间段（超过30天）的支持准则。 AKS 维护群集 etcd 元数据的备份，并可随时重新分配群集。 此重新分配可由使群集返回到支持的任何 PUT 操作（如升级或缩放到活动代理节点）启动。

## <a name="shared-responsibility"></a>共担责任

创建群集时，客户定义 AKS 创建的 Kubernetes 辅助角色节点。 在这些节点上执行客户工作负荷。 客户拥有，可以查看或修改辅助角色节点。

由于客户群集节点执行专用代码并存储敏感数据，因此 Microsoft 支持部门只能以有限的方式访问这些数据。 Microsoft 支持部门不能登录到，不能在中执行命令，也不能查看这些节点的日志，无需快速获得客户权限或协助。

由于辅助角色节点是敏感的，因此，Microsoft 要非常小心地限制其后台管理。 在许多情况下，即使 Kubernetes 主节点、etcd 和其他 Microsoft 托管组件发生故障，工作负荷也将继续运行。 漫不经心修改的工作节点可能会导致数据和工作负荷丢失，并可能会导致群集似乎。

## <a name="aks-support-coverage"></a>AKS 支持范围

Microsoft 为以下各项提供技术支持：

* 与 Kubernetes 服务提供并支持的所有 Kubernetes 组件的连接，例如 API 服务器。
* 管理、运行时间、QoS 和 Kubernetes 控制平面服务的操作（例如 Kubernetes 主节点、API 服务器、etcd 和 kube）。
* Etcd. 支持包括每30分钟自动执行的所有 etcd 数据的自动备份，以便进行灾难规划和群集状态还原。 不能直接向客户或用户提供这些备份。 它们可确保数据的可靠性和一致性。
* 适用于 Kubernetes 的 Azure 云提供程序驱动程序中的任何集成点。 其中包括与其他 Azure 服务（例如负载均衡器、永久性卷或网络）（Kubernetes 和 Azure CNI）的集成。
* 自定义控制平面组件（例如 Kubernetes API 服务器、etcd 和 kube）的问题或问题。
* 有关网络的问题，例如 Azure CNI、kubenet 或其他网络访问和功能问题。 问题可能包括 DNS 解析、数据包丢失、路由等。 Microsoft 支持各种网络方案：
  * 群集和关联组件中的 Kubenet （基本）和高级网络（Azure CNI）
  * 与其他 Azure 服务和应用程序的连接
  * 入口控制器和入口或负载平衡器配置
  * 网络性能和延迟

Microsoft 不为以下各项提供技术支持：

* 有关如何使用 Kubernetes 的问题。 例如，Microsoft 支持部门不会提供有关如何创建自定义入口控制器、使用应用程序工作负载或应用第三方或开源软件包或工具的建议。
  > [!NOTE]
  > Microsoft 支持部门可以建议 AKS 群集功能、自定义和优化（例如，Kubernetes 操作问题和过程）。
* 第三方开源项目，不是作为 Kubernetes 控制平面的一部分提供或部署在 AKS 群集中。 这些项目可能包括 Istio、Helm、Envoy 或其他项目。
  > [!NOTE]
  > Microsoft 可以为第三方开源项目（如 Helm 和 Kured）提供最大努力支持。 当第三方开源工具与 Kubernetes Azure 云提供程序或其他特定于 AKS 的 bug 集成时，Microsoft 支持 Microsoft 文档中的示例和应用程序。
* 第三方关闭源软件。 此软件可能包括安全扫描工具和网络设备或软件。
* 有关多云或多供应商构建的问题。 例如，Microsoft 不支持运行联合 multipublic 云供应商解决方案相关的问题。
* [AKS 文档](https://docs.microsoft.com/azure/aks/)中所列的网络自定义。
  > [!NOTE]
  > Microsoft 支持与网络安全组（Nsg）相关的问题和 bug。 例如，Microsoft 支持部门可以回答有关更新 NSG 失败或意外的 NSG 或负载均衡器行为的问题。

## <a name="aks-support-coverage-for-worker-nodes"></a>辅助角色节点的 AKS 支持范围

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>AKS 工作节点的 Microsoft 责任

Microsoft 和客户分担 Kubernetes 工作节点的责任，其中：

* 基本操作系统映像具有必需的添加件（如监视和网络代理）。
* 辅助角色节点会自动接收 OS 修补程序。
* 将自动修正在辅助角色节点上运行的 Kubernetes 控制平面组件的问题。 组件包括：
  * Kube-proxy
  * 向 Kubernetes 主组件提供通信路径的网络隧道
  * Kubelet
  * Docker 或小鲸鱼守护程序

> [!NOTE]
> 在辅助角色节点上，如果控制平面组件不可操作，则 AKS 团队可能需要重新启动单个组件或整个工作节点。 这些重新启动操作会自动执行，并为常见问题提供自动修正。 这些重启仅发生在_节点_级别，而不会在群集上发生，除非出现紧急维护或服务中断。

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>AKS 工作节点的客户责任

Microsoft 不会自动重新启动工作节点以应用操作系统级修补程序。 尽管 OS 修补程序会交付到辅助角色节点，但*客户*负责重新启动辅助角色节点以应用所做的更改。 共享库、稳定状态混合驱动器（SSHD）等守护程序以及系统或操作系统级别的其他组件会自动进行修补。

客户负责执行 Kubernetes 升级。 他们可以通过 Azure 控制面板或 Azure CLI 执行升级。 这适用于包含对 Kubernetes 的安全或功能改进的更新。

> [!NOTE]
> 由于 AKS 是一项*托管服务*，因此其最终目标包括删除修补程序、更新和日志收集的责任，以使服务管理更完整且不需要进行动手操作。 随着服务的端到端管理的容量增加，将来的版本可能会省略某些功能（例如，节点重新启动和自动修补）。

### <a name="security-issues-and-patching"></a>安全问题和修补

如果在 AKS 的一个或多个组件中发现了安全漏洞，则 AKS 团队将修补所有受影响的群集以缓解此问题。 或者，团队将向用户授予升级指导。

对于安全漏洞影响的辅助角色节点，如果提供零停机修补程序，AKS 团队将应用该修补程序并通知用户此项更改。

当安全修补程序需要重新启动工作节点时，Microsoft 会通知客户此要求。 客户负责重新启动或更新以获取群集修补程序。 如果用户不按照 AKS 指导应用修补程序，则其群集将继续容易受到安全问题的影响。

### <a name="node-maintenance-and-access"></a>节点维护和访问

辅助角色节点是一个共享的责任，由客户所有。 因此，客户可以登录到其辅助角色节点，并进行可能有害的更改，例如内核更新和安装或删除包。

如果客户进行破坏性更改或导致控制面组件脱机或变为不正常，AKS 将检测到此故障，并自动将工作节点还原到以前的工作状态。

尽管客户可以登录和更改工作节点，但不建议这样做，因为更改会使群集似乎。

## <a name="network-ports-access-and-nsgs"></a>网络端口、access 和 Nsg

作为托管服务，AKS 具有特定的网络和连接要求。 与普通 IaaS 组件的要求相比，这些要求不太灵活。 在 AKS 中，自定义 NSG 规则、阻止特定端口（例如，使用防火墙规则阻止出站端口 443）以及将 URL 加入允许列表等操作可能导致群集不受支持。

> [!NOTE]
> 目前，AKS 不允许你完全锁定群集的出口流量。 若要控制群集可用于出站流量的 Url 和端口的列表，请参阅[限制传出流量](limit-egress-traffic.md)。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Alpha 和 beta Kubernetes 功能不受支持

AKS 仅支持上游 Kubernetes 项目中的稳定功能。 除非另有说明，否则 AKS 不支持上游 Kubernetes 项目中提供的 alpha 和 beta 版功能。

在两种情况下，alpha 或 beta 版功能可能会在正式发布之前推出：

* 客户已与 AKS 产品、支持或工程团队合作，并已要求尝试这些新功能。
* 这些功能已[通过功能标志启用](https://github.com/Azure/AKS/blob/master/previews.md)。 客户必须明确选择使用这些功能。

## <a name="preview-features-or-feature-flags"></a>预览功能或功能标志

对于需要扩展测试和用户反馈的功能，Microsoft 发布了功能标志后的新预览版功能或功能。 请考虑将这些功能作为预发布或 beta 版功能。

预览功能或功能标志功能并非适用于生产。 Api 和行为的不断变化、bug 修复和其他更改可能会导致不稳定的群集和停机时间。

公共预览版中的功能处于 "最大努力" 的支持下，因为这些功能处于预览阶段，不适用于生产环境，并且仅在营业时间内 AKS 技术支持团队提供支持。 有关其他信息，请参阅：

* [Azure 支持常见问题](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> 预览功能在 Azure*订阅*级别生效。 请勿在生产订阅上安装预览功能。 在生产订阅上，预览功能可以更改默认的 API 行为，并影响常规操作。

## <a name="upstream-bugs-and-issues"></a>上游 bug 和问题

鉴于上游 Kubernetes 项目的开发速度，bug 总是出现。 其中一些错误无法在 AKS 系统中进行修补或处理。 相反，bug 修复需要更大的上游项目修补程序（例如 Kubernetes、节点或工作线程操作系统以及内核）。 对于 Microsoft 拥有的组件（如 Azure 云提供商），AKS 和 Azure 人员承诺解决社区中的上游问题。

当技术支持问题由一个或多个上游错误引起，AKS 支持和工程团队将：

* 标识并链接上游 bug，并提供任何支持详细信息，以帮助解释此问题对群集或工作负荷的影响。 客户接收到所需的存储库的链接，以便他们能够观看问题并查看新版本何时会提供修补程序。
* 提供可能的解决方法或缓解措施。 如果可缓解此问题，则会在 AKS 存储库中存档一个[已知问题](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)。 已知问题的归档说明如下：
  * 此问题包括到上游 bug 的链接。
  * 解决方法以及解决方案的其他持久性的详细信息。
  * 根据上游发布节奏，为问题包含时间线。
