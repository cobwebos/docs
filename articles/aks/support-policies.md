---
title: Azure Kubernetes 服务 (AKS) 的支持策略
description: 了解 Azure Kubernetes 服务 (AKS) 预览版（或者 alpha 或 beta 版）中的支持策略、共担责任和功能。
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: be80c0d5999ed06bcc505ec5737e7e416d28b981
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050684"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes 服务的支持策略

本文提供有关 Azure Kubernetes 服务 (AKS) 的技术支持策略和限制的详细信息。 本文还详细介绍了工作器节点管理、托管控制平面组件、第三方开源组件，以及安全性或修补程序管理。

## <a name="service-updates-and-releases"></a>服务更新和版本

* 有关发行信息，请参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)。
* 有关预览版功能的信息，请参阅 [AKS 预览版功能和相关项目](https://github.com/Azure/AKS/blob/master/previews.md)。

## <a name="managed-features-in-aks"></a>AKS 中的托管功能

基本的基础结构即服务 (IaaS) 云组件（例如计算或网络组件）可让用户访问低级别的控件机制和自定义选项。 相比之下，AKS 提供统包式的 Kubernetes 部署，为客户提供一组通用配置和所需的功能。 AKS 客户可用的自定义功能、部署和其他选项有限。 这些客户不需要考虑或直接管理 Kubernetes 群集。

借助 AKS，客户可以获得一个完全托管型的控制平面。 该控制平面包含客户操作以及向最终用户提供 Kubernetes 群集的全部所需组件和服务。 所有 Kubernetes 组件都由 Microsoft 维护和操作。

Microsoft 通过控制窗格管理和监视以下组件：

* Kubelet 或 Kubernetes API 服务器
* Etcd 或兼容的键-值存储，提供服务质量 (QoS)、可伸缩性和运行时
* DNS 服务（例如 kube-dns 或 CoreDNS）
* Kubernetes 代理或网络

AKS 不是完全托管型的群集解决方案。 某些组件（例如工作器节点）实行分担责任制，用户必须帮助维护 AKS 群集。 例如，必须提供用户输入才能应用工作器节点操作系统 (OS) 安全修补程序。

这些服务是托管的，因为 Microsoft 和 AKS 团队负责部署和操作服务，并负责维护服务的可用性和功能。 客户无法改动这些托管组件。 Microsoft 限制了自定义，以确保一致且可缩放的用户体验。 有关完全可自定义的解决方案，请参阅 [AKS 引擎](https://github.com/Azure/aks-engine)。

## <a name="shared-responsibility"></a>共担责任

创建群集时，客户需定义 AKS 创建的 Kubernetes 工作器节点。 客户工作负荷将在这些节点上执行。 客户拥有并可以查看或修改工作器节点。

由于客户群集节点执行私有代码并存储敏感数据，因此 Microsoft 支持部门只能以受限的方式访问这些节点。 没有直接的客户许可或帮助，Microsoft 支持人员无法登录这些节点、无法在其中执行命令，也无法查看其日志。

由于工作器节点很敏感，因此，Microsoft 要非常小心地限制其后台管理。 在许多情况下，即使 Kubernetes 主节点、etcd 和其他 Microsoft 托管组件失败，你的工作负荷也会继续运行。 不小心修改工作器节点可能会导致数据和工作负荷丢失，并使群集变得不受支持。

## <a name="aks-support-coverage"></a>AKS 支持范围

Microsoft 为以下各项提供技术支持：

> [!NOTE]
> Microsoft/AKS 采取的任何群集操作都是以内置 Kubernetes 角色 `aks-service` 和内置角色绑定来进行的 `aks-service-rolebinding` 。 此角色允许 AKS 对群集问题进行故障排除，但是不能修改权限，也不能创建角色或角色绑定，或其他高权限操作。 仅在具有实时 (JIT) 访问权限的活动支持票证下启用角色访问。

* 连接到 Kubernetes 服务提供和支持的所有 Kubernetes 组件，例如 API 服务器。
* Kubernetes 控制平面服务（例如 Kubernetes 主节点、API 服务器、etcd 和 kube-dns）的管理、运行时间、QoS 和操作。
* Etcd。 支持包括每隔 30 分钟以透明方式自动备份所有 etcd 数据，以实现灾难规划和群集状态还原。 客户或用户不可直接使用这些备份。 这些备份用于确保数据的可靠性和一致性。 Etcd。 不支持按需回滚或还原功能。
* 适用于 Azure 云提供程序驱动程序中的任何集成点。 这包括与负载均衡器、永久性卷或网络组件（Kubernetes 和 Azure CNI）等其他 Azure 服务的集成。
* 有关控制平面组件（例如 Kubernetes API 服务器、etcd 和 kube-dns）等控制平面组件的自定义问题。
* 有关网络组件（例如 Azure CNI、kubenet）的问题，或其他网络访问和功能问题。 问题可能包括 DNS 解析、数据包丢失、路由等。 Microsoft 为各种网络方案提供支持：
  * 群集和关联组件中的 Kubenet（基本）和高级网络 (Azure CNI)
  * 连接到其他 Azure 服务和应用程序
  * 入口控制器以及入口或负载均衡器配置
  * 网络性能和延迟

Microsoft 不为以下各项提供技术支持：

* 有关 Kubernetes 用法的问题。 例如，对于如何创建自定义入口控制器、如何使用应用程序工作负荷，或者如何应用第三方的或开源的软件包或工具，Microsoft 支持部门不提供建议。
  > [!NOTE]
  > Microsoft 支持部门可以针对 AKS 群集功能、自定义和优化（例如，Kubernetes 操作问题和过程）提供建议。
* 不是作为 Kubernetes 控制平面的一部分提供的，或者不是在 AKS 群集中部署的第三方开源项目。 这些项目可能包括 Istio、Helm、Envoy 等等。
  > [!NOTE]
  > Microsoft 会尽力为 Helm 和 Kured 等第三方开源项目提供支持。 当第三方开源工具与 Kubernetes 集成时，如果出现了特定于 Azure 云提供商或其他 AKS 的 bug，Microsoft 会为来自 Microsoft 文档的示例和应用程序提供支持。
* 第三方闭源软件。 此类软件可能包括安全扫描工具以及网络设备或软件。
* 有关多云或多供应商扩建的问题。 例如，Microsoft 不为与运行联合多公有云供应商解决方案相关的问题提供支持。
* [AKS 文档](./index.yml)中未列出的网络自定义。
  > [!NOTE]
  > Microsoft 可以为与网络安全组 (NSG) 相关的问题和 bug 提供支持。 例如，Microsoft 支持部门可以回答有关 NSG 更新失败或者 NSG 或负载均衡器行为异常的问题。

## <a name="aks-support-coverage-for-worker-nodes"></a>针对工作器节点的 AKS 支持范围

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Microsoft 对 AKS 工作器节点的责任

Microsoft 和客户分担对 Kubernetes 工作器节点的责任，其中：

* 基本 OS 映像收到了必需的新增功能（例如监视和网络代理）。
* 工作器节点自动收到了 OS 修补程序。
* 在工作器节点上运行的 Kubernetes 控制平面组件的问题可自动得到解决。 组件包括：
  * Kube 代理
  * 为 Kubernetes 主控组件提供通信路径的网络隧道
  * Kubelet
  * Docker 或 Moby 守护程序

> [!NOTE]
> 在工作器节点上，如果某个控制平面组件无法正常运行，则 AKS 团队可能需要重新启动单个组件或整个工作器节点。 这些重启操作会自动执行，并为常见问题提供自动修正。 这些重新启动仅发生在_节点_级别，而不是群集，除非是紧急维护或停机。

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>客户对 AKS 工作器节点承担的责任

Microsoft 不会自动重启工作器节点来应用操作系统级修补程序。 尽管 OS 修补程序是为工作器节点交付的，但客户需负责重新启动工作器节点来应用更改。 系统或 OS 级别的共享库、守护程序（例如固态混合驱动器 (SSHD)）和其他组件将自动得到修补。

客户负责执行 Kubernetes 升级。 他们可以通过 Azure 控制面板或 Azure CLI 执行升级。 这适用于包含 Kubernetes 的安全或功能改进的更新。

#### <a name="user-customization-of-worker-nodes"></a>对工作器节点进行的用户自定义
> [!NOTE]
> AKS 工作器节点在 Azure 门户中显示为常规 Azure IaaS 资源。 但是这些虚拟机被部署到自定义的 Azure 资源组（前缀为 MC\\*）中。 可以在 AKS 工作器节点的基础配置的基础上对其进行扩充。 例如，你可以使用安全外壳 (SSH) 更改 AKS 工作器节点，就像更改普通虚拟机一样。 但是，你无法更改基础操作系统映像。 自定义更改在升级、缩放、更新或重启后可能不会保留。 **但是**，在 AKS API 的带外和作用域外进行更改会导致 AKS 群集变得不受支持。 请避免更改工作器节点，除非 Microsoft 支持部门指示你进行更改。

发出上面定义的不受支持的操作（如所有代理节点的带外解除分配）会导致群集不受支持。 对于已配置了“停止支持”规则以将支持期限延长至等于或超过 30 天的控制平面，AKS 保留了将其存档的权利。 AKS 维护群集 etcd 元数据的备份，并可轻松地重新分配群集。 此重新分配可以由任何使群集重获支持的 PUT 操作（例如升级或缩放到活动代理节点）启动。

AKS 代表客户管理工作器节点的生命周期和操作 - **不支持**修改与工作器节点关联的 IaaS 资源。 不支持的操作的一个示例是通过 VMSS 门户或 VMSS API 手动更改 VMSS 上的配置来自定义节点池 VM 规模集。
 
对于特定于工作负荷的配置或包，AKS 建议使用 [Kubernetes daemonset](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)。

使用 Kubernetes 特权 daemonset 和 init 容器，客户可以在群集工作器节点上调整/修改或安装第三方软件。 此类自定义的示例包括添加自定义安全扫描软件或更新 sysctl 设置。

虽然当上述要求适用时这是建议的路径，但 AKS 工程和支持部门无法协助排查或诊断损坏性/非功能性修改的问题，也无法协助排查或诊断由于客户部署的 daemonset 而导致节点不可用的修改的问题。

> [!NOTE]
> 作为一项托管服务，AKS 的最终目标包括免除用户在修补、更新和日志收集方面的职责，使服务管理变得更完整且无需人工干预。 随着服务的端到端管理能力的增强，将来的版本可能会省略一些功能（例如，节点重启和自动修补）。

### <a name="security-issues-and-patching"></a>安全问题和修补

如果在 AKS 的一个或多个组件中找到安全缺陷，AKS 团队将修补所有受影响的群集以缓解此问题。 或者，AKS 团队将为用户提供升级指导。

对于安全缺陷所影响到的工作器节点，如果有不会造成任何停机的修补程序可用，则 AKS 团队会应用该修补程序，并通知用户发生了更改。

当安全修补程序要求工作器节点重启时，Microsoft 会将此要求通知给客户。 客户负责重新启动或更新以获取群集修补程序。 如果用户未根据 AKS 的指导应用修补程序，其群集仍会受到安全问题的影响。

### <a name="node-maintenance-and-access"></a>节点维护和访问

工作器节点由客户拥有，其责任由客户与 Azure 共同分担。 因此，客户可能会登录到其工作器节点并进行潜在有害的更改，例如更新内核，以及安装或删除包。

如果客户做出了破坏性的更改或者导致控制平面组件脱机或异常，则 AKS 会检测到这种故障，并将工作器节点自动还原到以前的正常运行状态。

尽管客户可以登录并更改工作器节点，但我们不建议这样做，因为做出更改可能会导致群集不受支持。

## <a name="network-ports-access-and-nsgs"></a>网络端口、访问和 NSG

作为一项托管服务，AKS 在网络和连接方面存在特定的要求。 与普通 IaaS 组件的要求相比，这些要求不太灵活。 在 AKS 中，自定义 NSG 规则、阻止特定端口（例如，使用阻止出站端口443的防火墙规则）和将 Url 添加到允许列表等操作都可以使群集似乎。

> [!NOTE]
> 目前，AKS 不允许完全锁定群集的出口流量。 若要控制可供群集用于出站流量的 URL 和端口的列表，请参阅[限制出口流量](limit-egress-traffic.md)。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不受支持的 alpha 和 beta Kubernetes 功能

AKS 仅支持上游 Kubernetes 项目中的稳定功能。 除非另有说明，否则，AKS 不支持上游 Kubernetes 项目中提供的 alpha 和 beta 功能。

在以下两种情况下，alpha 或 beta 功能可能在正式版发布之前便已推出：

* 客户已与 AKS 产品、支持或工程团队会谈，团队请求他们尝试这些新功能。
* 这些功能已[通过功能标志启用](https://github.com/Azure/AKS/blob/master/previews.md)。 客户必须明确选择使用这些功能。

## <a name="preview-features-or-feature-flags"></a>预览功能或功能标志

对于需要进一步测试并收集用户反馈的功能，Microsoft 会发布新的预览版功能或带功能标志的功能。 请将这些功能视为预发行版或 beta 功能。

预览功能或功能标志功能不适用于生产环境。 API 和行为的不断变化、bug 修复和其他更改可能会导致群集不稳定和停机。

公共预览版中的功能受到“尽力而为”支持，因为这些功能处于预览状态，而不是用于生产环境，并且仅在工作时间由 AKS 技术支持团队提供支持。 有关其他信息，请参阅：

* [Azure 支持常见问题](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> 预览功能在 Azure 订阅级别生效。 请勿在生产订阅上安装预览功能。 在生产订阅上，预览功能可以更改默认 API 行为并影响常规操作。

## <a name="upstream-bugs-and-issues"></a>上游 bug 和问题

由于上游 Kubernetes 项目的开发速度，不可避免地会出现 bug。 其中的某些 bug 无法在 AKS 系统内部得到修补或解决。 bug 修复需要对上游项目（例如 Kubernetes、节点或辅助角色操作系统以及内核）应用较大的修补程序。 对于 Microsoft 拥有的组件（例如 Azure 云提供商），AKS 和 Azure 人员致力于在社区中修复上游问题。

如果判定某个技术支持问题是一个或多个上游 bug 的根本原因，则 AKS 支持和工程团队将会：

* 使用任何支持详细信息来识别并链接上游 bug，以帮助解释为何此问题会影响群集或工作负荷。 客户会收到所需存储库的链接，以便可以观察问题，并了解何时有新的版本可以提供修复措施。
* 提供潜在的解决方法或缓解措施。 如果该问题可以缓解，则会在 AKS 存储库中创建[已知问题](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)的备案。 已知问题备案将会解释：
  * 该问题，包括上游 bug 的链接。
  * 解决方法，以及有关解决方案升级或其他持久化措施的详细信息。
  * 问题包含内容的大致时间线，根据上游版本发布频率提供。
