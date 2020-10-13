---
title: Azure Kubernetes 服务 (AKS) 的支持策略
description: 了解 Azure Kubernetes 服务 (AKS) 预览版（或者 alpha 或 beta 版）中的支持策略、共担责任和功能。
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 86b1c0bba30b41a2ee17cfbdf05286c4d2b3fb8a
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91892704"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes 服务的支持策略

本文提供有关 Azure Kubernetes 服务 (AKS) 的技术支持策略和限制的详细信息。 本文还详细介绍了代理节点管理、托管控制平面组件、第三方开源组件以及安全性或修补程序管理。

## <a name="service-updates-and-releases"></a>服务更新和版本

* 有关发行信息，请参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)。
* 有关预览版功能的信息，请参阅 [AKS 预览版功能和相关项目](https://awesomeopensource.com/projects/aks?categoryPage=11)。

## <a name="managed-features-in-aks"></a>AKS 中的托管功能

基础结构即服务 (IaaS) 云组件，如计算或网络组件，允许访问低级别的控件和自定义选项。 与此相反，AKS 提供了一个全包式 Kubernetes 部署，为群集提供了所需的一组常用配置和功能。 作为 AKS 用户，你的自定义和部署选项受到限制。 在 exchange 中，无需担心或直接管理 Kubernetes 群集。

使用 AKS，可以获得完全托管的 *控制平面*。 控制平面包含操作所需的所有组件和服务，并向最终用户提供 Kubernetes 群集。 所有 Kubernetes 组件都由 Microsoft 维护和操作。

Microsoft 通过控制窗格管理和监视以下组件：

* Kubelet 或 Kubernetes API 服务器
* Etcd 或兼容的键-值存储，提供服务质量 (QoS)、可伸缩性和运行时
* DNS 服务（例如 kube-dns 或 CoreDNS）
* Kubernetes 代理或网络
* 在 kube 命名空间中运行的任何其他加载项或系统组件

AKS 不是平台即服务 (PaaS) 解决方案。 某些组件（如代理节点）具有 *共同责任*，用户必须在该位置帮助维护 AKS 群集。 例如，需要用户输入才能将代理节点操作系统 (操作系统) 安全修补程序中。

这些服务是托管的，因为 Microsoft 和 AKS 团队负责部署和操作服务，并负责维护服务的可用性和功能。 客户无法更改这些托管组件。 Microsoft 限制了自定义，以确保一致且可缩放的用户体验。 如需一个可以完全地进行自定义的解决方案，请参阅 [AKS 引擎](https://github.com/Azure/aks-engine)。

## <a name="shared-responsibility"></a>共担责任

创建群集时，可定义 AKS 创建的 Kubernetes 代理节点。 在这些节点上执行工作负荷。

由于代理节点会执行专用代码并存储敏感数据，因此 Microsoft 支持部门只能以非常有限的方式访问这些数据。 Microsoft 支持部门不能登录到，不能在中执行命令，也不能查看这些节点的日志。

使用任意 IaaS Api 直接对代理节点进行的任何修改都会呈现群集似乎。 对代理节点所做的任何修改都必须使用 kubernetes 等本机机制完成 `Daemon Sets` 。

同样，虽然你可以将任何元数据添加到群集和节点（如标记和标签），但更改任何系统创建的元数据将导致群集不受支持。

## <a name="aks-support-coverage"></a>AKS 支持范围

Microsoft 为以下示例提供技术支持：

* 连接到 Kubernetes 服务提供和支持的所有 Kubernetes 组件，例如 API 服务器。
* Kubernetes 控制平面服务 (的管理、运行时间、QoS 和操作，) 控制平面、API 服务器、etcd 和 coreDNS，例如。
* Etcd 数据存储。 支持包括每隔 30 分钟以透明方式自动备份所有 etcd 数据，以实现灾难规划和群集状态还原。 这些备份不会直接供你或任何用户使用。 这些备份用于确保数据的可靠性和一致性。 Etcd。 不支持按需回滚或还原作为功能。
* 适用于 Azure 云提供程序驱动程序中的任何集成点。 这包括与负载均衡器、永久性卷或网络组件（Kubernetes 和 Azure CNI）等其他 Azure 服务的集成。
* 自定义控制平面组件（例如 Kubernetes API 服务器、etcd 和 coreDNS）的问题或问题。
* 有关网络组件（例如 Azure CNI、kubenet）的问题，或其他网络访问和功能问题。 问题可能包括 DNS 解析、数据包丢失、路由，等等。 Microsoft 为各种网络方案提供支持：
  * 使用托管 Vnet 或自定义 (的 Kubenet 和 Azure CNI 自带) 子网。
  * 连接到其他 Azure 服务和应用程序
  * 入口控制器以及入口或负载均衡器配置
  * 网络性能和延迟


> [!NOTE]
> Microsoft/AKS 所执行的任何群集操作都是经用户同意，在内置 Kubernetes 角色 `aks-service` 和内置角色绑定 `aks-service-rolebinding` 下执行的。 此角色允许 AKS 对群集问题进行故障排除和诊断，但不能修改权限，也不能创建角色或角色绑定，或者执行其他高特权操作。 仅在具有实时 (JIT) 访问权限的活动支持票证下启用角色访问。

对于以下示例，Microsoft 不提供技术支持：

* 有关 Kubernetes 用法的问题。 例如，对于如何创建自定义入口控制器、如何使用应用程序工作负荷，或者如何应用第三方的或开源的软件包或工具，Microsoft 支持部门不提供建议。
  > [!NOTE]
  > Microsoft 支持部门可以针对 AKS 群集功能、自定义和优化（例如，Kubernetes 操作问题和过程）提供建议。
* 不是作为 Kubernetes 控制平面的一部分提供的，或者不是在 AKS 群集中部署的第三方开源项目。 这些项目可能包括 Istio、Helm、Envoy 等等。
  > [!NOTE]
  > Microsoft 可以为第三方开放源代码项目（如 Helm）提供最大努力支持。 当第三方开源工具与 Kubernetes 集成时，如果出现了特定于 Azure 云提供商或其他 AKS 的 bug，Microsoft 会为来自 Microsoft 文档的示例和应用程序提供支持。
* 第三方闭源软件。 此类软件可能包括安全扫描工具以及网络设备或软件。
* [AKS 文档](./index.yml)中所列的网络自定义。


## <a name="aks-support-coverage-for-agent-nodes"></a>代理节点的 AKS 支持范围

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Microsoft AKS agent 节点的责任

Microsoft 和用户共享 Kubernetes 代理节点的责任，其中：

* 基本 OS 映像收到了必需的新增功能（例如监视和网络代理）。
* 代理节点会自动接收 OS 修补程序。
* 自动修正在代理节点上运行的 Kubernetes 控制平面组件的问题。 这些组件包括：
  * `Kube-proxy`
  * 为 Kubernetes 主控组件提供通信路径的网络隧道
  * `Kubelet`
  * `Moby` 或 `ContainerD`

> [!NOTE]
> 如果代理节点不可操作，AKS 可能会重新启动单个组件或整个代理节点。 这些重新启动操作会自动执行，并为常见问题提供自动修正。 如果要了解有关自动修正机制的详细信息，请参阅 [节点自动修复](node-auto-repair.md)

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>AKS 代理节点的客户责任

Microsoft 每周为映像节点提供修补程序和新映像，但默认情况下不会自动修补它们。 若要保持代理节点操作系统和运行时组件的修补，应保留常规 [节点映像升级](node-image-upgrade.md) 计划或自动执行。

同样，AKS 会定期发布新的 kubernetes 修补程序和次要版本。 这些更新可包含对 Kubernetes 的安全或功能改进。 你应负责保持群集的 kubernetes 版本更新，并根据 [AKS Kubernetes 支持版本策略](supported-kubernetes-versions.md)。

#### <a name="user-customization-of-agent-nodes"></a>用户自定义代理节点
> [!NOTE]
> AKS 代理节点在 Azure 门户中显示为常规 Azure IaaS 资源。 但这些虚拟机部署到自定义 Azure 资源组中， (通常以 MC_ \*) 为前缀。 你不能更改基础操作系统映像或使用 IaaS Api 或资源对这些节点进行任何直接自定义。 不通过 AKS API 执行的任何自定义更改都不会通过升级、缩放、更新或重新启动来持久保存。 避免对代理节点执行更改，除非 Microsoft 支持部门指示你进行更改。

AKS 管理代理节点的生命周期和操作（代表你修改代理节点关联的 IaaS 资源 **）。** 例如，不支持的操作是通过虚拟机规模集门户或 API 手动更改配置来自定义节点池虚拟机规模集。
 
对于特定于工作负荷的配置或包，AKS 建议使用[Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)。

使用 Kubernetes 特权 `daemon sets` 和 init 容器，可以在群集代理节点上调整/修改或安装第三方软件。 此类自定义的示例包括添加自定义安全扫描软件或更新 sysctl 设置。

尽管上述要求适用，但建议使用此路径，但 AKS 工程和支持人员无法帮助排查和诊断因自定义部署而导致节点不可用的修改 `daemon set` 。

### <a name="security-issues-and-patching"></a>安全问题和修补

如果在 AKS 的一个或多个托管组件中发现了安全漏洞，则 AKS 团队会修补所有受影响的群集以缓解此问题。 或者，AKS 团队将为用户提供升级指导。

对于受安全缺陷影响的代理节点，Microsoft 将通知你，详细了解 (通常节点映像升级或群集修补升级) 的影响和解决或缓解安全问题的步骤。

### <a name="node-maintenance-and-access"></a>节点维护和访问

虽然你可以登录和更改代理节点，但不建议执行此操作，因为更改可能导致群集似乎。

## <a name="network-ports-access-and-nsgs"></a>网络端口、访问和 NSG

只能自定义子网中的 Nsg。 你不能在托管子网或代理节点的 NIC 级别上自定义 Nsg。 AKS 对特定终结点的出口要求，控制出口并确保必要的连接性，请参阅 [限制传出流量](limit-egress-traffic.md)。

## <a name="stopped-or-de-allocated-clusters"></a>停止或取消分配的群集

如前文所述，通过 IaaS Api/CLI/门户手动取消分配所有群集节点会导致群集失去支持。 停止/解除分配所有节点的唯一支持方法是 [停止 AKS 群集](start-stop-cluster.md#stop-an-aks-cluster)，这会将群集状态保留最多12个月。

超过12个月的停止的群集将不再保留状态。 

在 AKS Api 外解除分配的群集不保证状态保留。 处于此状态的群集的控制平面将在30天后存档，并在12个月后被删除。

对于已配置了“停止支持”规则以将支持期限延长至等于或超过 30 天的控制平面，AKS 保留了将其存档的权利。 AKS 维护群集 etcd 元数据的备份，并可轻松地重新分配群集。 此重新分配可以由任何使群集重获支持的 PUT 操作（例如升级或缩放到活动代理节点）启动。

如果订阅被挂起或删除，则群集的控制面和状态将在90天后被删除。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不受支持的 alpha 和 beta Kubernetes 功能

AKS 仅支持上游 Kubernetes 项目中的稳定和 beta 版功能。 除非另有说明，否则 AKS 不支持上游 Kubernetes 项目中提供的任何 alpha 功能。

## <a name="preview-features-or-feature-flags"></a>预览功能或功能标志

对于需要扩展测试和用户反馈的功能，Microsoft 发布了功能标志后的新预览版功能或功能。 请将这些功能视为预发行版或 beta 功能。

预览功能或功能标志功能不适用于生产环境。 API 和行为的不断变化、bug 修复和其他更改可能会导致群集不稳定和停机。

公共预览版中的功能受到“尽力而为”支持，因为这些功能处于预览状态，而不是用于生产环境，并且仅在工作时间由 AKS 技术支持团队提供支持。 有关详细信息，请参阅：

* [Azure 支持常见问题](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>上游 bug 和问题

由于上游 Kubernetes 项目的开发速度，不可避免地会出现 bug。 其中的某些 bug 无法在 AKS 系统内部得到修补或解决。 相反，bug 修复需要对上游项目 (更大的修补程序，如 Kubernetes、节点或代理操作系统以及内核) 。 对于 Microsoft 拥有的组件（例如 Azure 云提供商），AKS 和 Azure 人员致力于在社区中修复上游问题。

如果某个技术支持问题从根本上来说是一个或多个上游 bug 导致的，则 AKS 支持和工程团队会采取以下措施：

* 使用任何支持详细信息来识别并链接上游 bug，以帮助解释为何此问题会影响群集或工作负荷。 客户会收到所需存储库的链接，以便可以观察问题，并了解何时有新的版本可以提供修复措施。
* 提供可能的解决方法或缓解措施。 如果该问题可以缓解，则会在 AKS 存储库中创建[已知问题](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)的备案。 已知问题备案将会解释：
  * 该问题，包括上游 bug 的链接。
  * 解决方法，以及有关解决方案升级或其他持久化措施的详细信息。
  * 问题包含内容的大致时间线，根据上游版本发布频率提供。
