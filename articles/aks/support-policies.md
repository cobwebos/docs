---
title: Azure Kubernetes 服务 (AKS) 支持策略
description: 了解有关 Azure Kubernetes 服务 (AKS) 的支持策略、 共担责任、 预览/Alpha/Beta 功能的信息。
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032462"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Azure Kubernetes 服务 (AKS) 支持策略

本文提供有关 AKS 技术支持策略，限制，详细信息和详细信息，包括辅助角色节点管理，托管控制平面组件、 第三方开放源代码组件和安全 / 修补程序管理。

## <a name="service-updates--releases"></a>服务更新和版本

* 发布信息，请参阅[AKS 发行说明][1]
* 公共预览版中的功能，请参阅[AKS 预览功能和相关项目][2]

## <a name="what-is-managed"></a>什么是托管

与基 IaaS 云组件，如计算或网络，它们公开低级别的控件和用户可以利用的自定义选项，不同 AKS 服务提供了表示一组常用的配置的统包式 Kubernetes 部署和适用于 Kubernetes 所需的功能。 使用此服务的客户具有有限的数量的自定义、 部署和其他选项。 这也意味着，客户无需担心或直接管理 Kubernetes 群集。

通过 AKS，客户获取的完全托管型**控制平面**– 控制平面其中包含所有组件和所需运行，并向最终用户提供 Kubernetes 群集的服务。 所有 Kubernetes 组件维护，由 Microsoft 运营。

与托管**控制平面**管理和监视 Microsoft 的以下组件：

* Kubelet / Kubernetes API 服务器
* Etcd 或兼容的键/值存储 – 包括质量的服务、 可伸缩性和运行时
* DNS 服务 (例如，kube dns / CoreDNS)
* Kubernetes 代理/网络

AKS 不是 100 %managed**群集**解决方案。 某些组件 （如辅助角色节点） 具有某些**共享责任**措施维护 AKS 群集，需要用户交互的情况。 例如，辅助角色的节点 OS 安全修补程序应用程序。

 **托管**，意味着 Microsoft 和 AKS 团队将部署时，操作原理，并负责的可用性和这些服务的功能。 **客户无法更改这些组件**。 自定义受到限制，以确保一致且可缩放的用户体验。 完全可自定义解决方案，请参阅[AKS 引擎](https://github.com/Azure/aks-engine)。

> [!NOTE]
> 务必要了解 Azure Kubernetes 服务辅助角色节点显示在 Azure 门户中，为常规 Azure IaaS 资源，尽管这些虚拟机部署到自定义 Azure 资源组 (前缀为 MC\\*)。 用户可能会更改它们，SSH 到它们就像正常的虚拟机 （不能但是，更改基本的操作系统映像，并更改可能未在更新保持不变或重新启动），并可以将其他 Azure 资源连接到它们，或修改它们。 **但是，这样的带外管理和自定义项意味着 AKS 群集本身可能不支持。除非 Microsoft 支持，请避免辅助角色节点更改类型。**

## <a name="what-is-shared-responsibility"></a>什么被共享责任

在创建群集时，AKS 创建多个客户定义的 Kubernetes 辅助角色节点。 这些节点，如所述是客户工作负荷的执行位置。 客户拥有并可以查看或修改这些辅助角色节点。

因为这些节点执行专用代码，并将敏感数据存储，具有 Microsoft 支持部门**有限的访问权限**到所有客户群集节点。 Microsoft 支持部门不能登录到、 执行命令，或查看而无需执行的调试命令的支持团队的指导 express 客户权限和/或帮助这些节点的日志。

由于这些辅助角色节点的敏感特性，Microsoft 不抽取格外注意以限制这些节点的后台管理。 即使 Kubernetes 主节点、 etcd 和其他组件故障 （由 Microsoft 管理） 工作负荷将继续在许多情况下运行。 如果不小心修改了辅助角色节点，它会导致工作负荷和/或数据丢失，并且使群集不支持。

## <a name="azure-kubernetes-service-support-coverage"></a>Azure Kubernetes 服务支持覆盖范围

**Microsoft 提供以下技术支持：**

* 连接到 Kubernetes 的所有组件提供和支持的 Kubernetes 服务 （例如 API 服务器中）
* 管理、 运行时间、 QoS 和操作 Kubernetes 控制平面服务 (Kubernetes 主节点，API 服务器 etcd，kube dns 示例。
* Etcd 支持包括 etcd 的所有数据的自动、 透明备份灾难规划和群集还原状态每隔 30 分钟。 这些备份不是直接提供给客户/用户，用于确保数据可靠性和一致性
* 任何集成点中适用于 Kubernetes 的 Azure 云提供程序驱动程序等集成到其他 Azure 服务 （如负载均衡器、 永久性卷，网络 （Kubernetes 和 Azure CNI））
* 问题或事宜如 Kubernetes API 服务器、 etcd 和 kube dns 控制平面组件的自定义。
* 有关网络连接主题，例如 Azure CNI、 Kubenet 或其他网络问题 （DNS 解析、 数据包丢失、 路由等） 的访问权限和功能问题。
  * 支持的网络方案中的群集和关联的组件、 连接到其他 Azure 服务和应用程序包括 Kubenet (Basic) 和高级网络 (Azure CNI)。 此外，由 Microsoft 支持入口控制器和入口/负载均衡器配置、 网络性能和延迟。

**Microsoft 不提供以下技术支持：**

* 咨询 /"操作指南"使用 Kubernetes 问题，例如如何创建自定义入口控制器，应用程序工作负荷问题和第三方/OSS 包或工具已超出范围。
  * 适用于 AKS 的咨询票证群集功能，自定义项，优化 – 例如 Kubernetes 操作问题/说明 tos 是作用域内。
* 不作为 Kubernetes 的一部分提供的第三方开放源代码项目控制平面，或使用 AKS 群集中，如 Istio、 Helm、 Envoy，以及其他部署。
  * 对于第三方开放源代码项目，如 Helm 和 Kured，有关示例和应用程序提供 Microsoft 文档和 Kubernetes Azure 云提供程序与该第三方开源工具集成，其中提供最佳支持或其他特定于 AKS 的 bug。
* 第三方关闭源软件 – 这可能包括安全扫描工具，网络设备或软件。
* 不支持"多云"或多供应商的生成版本有关的问题，例如运行联合多公有云供应商解决方案不受支持。
* 特定的网络自定义项，而非官方中所述[AKS 文档][3]。
  > [!NOTE]
  > 支持问题和解决网络安全组的 bug。 例如，支持可以回答关于 Nsg 无法更新或意外的 NSG 或负载均衡器行为问题。

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Azure Kubernetes 服务支持覆盖范围 （辅助角色节点）

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Azure Kubernetes 服务辅助角色节点的 Microsoft 职责

如中所述`shared responsibility`部分中，Kubernetes 辅助角色节点属于某一联合责任模型，其中：

* 基本操作系统映像提供所需的新增功能 （如监视和网络代理）
* 到辅助角色节点的操作系统修补程序的自动交付
* 自动修正问题与 Kubernetes 控制平面组件，如辅助角色节点上运行：
  * kube-proxy
  * 网络隧道提供到 Kubernetes 的通信路径的主机组件
  * kubelet
  * docker/小鲸鱼守护程序

> [!NOTE]
> 如果辅助角色节点上不可操作状态控制平面组件，AKS 团队可能需要重新启动整个辅助角色节点来解决此问题。 这是代表用户执行，除非客户升级由 (due 客户活动的工作负荷和数据访问)，否则不会执行。 只要可能 AKS 人员将工作以进行任何所需重新启动非应用程序影响。

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Azure Kubernetes 服务辅助角色节点的客户职责

**Microsoft 将不执行操作：**

- 自动重启辅助角色节点的 OS 级别才会生效的修补程序 **（目前，请参阅下文）**

操作系统修补程序传递给辅助角色节点，但是它 **，客户有责任**重启辅助角色节点的更改才会生效。 此自动修补还包括共享的库，如 SSHD，守护程序和其他系统/OS 级别的组件。

对于 Kubernetes 升级**客户需要负责执行升级**通过 Azure 控制面板或 Azure CLI。 这适用于更新包含安全或对 Kubernetes 的功能改进。

> [!NOTE]
> AKS 原样`managed service`我们的服务的最终目标包括删除修补程序、 更新、 日志收集和详细信息，使其更多服务可以完全托管且无需干预的责任。 随着我们功能来执行操作的端到端管理的增加，将来的版本中可能会删除这些项 （节点重新启动，自动修补）。

### <a name="security-issues-and-patching"></a>安全问题和修补

在某些情况下 （例如 CVEs)，可能在一个或多个 AKS 服务的组件中找到安全漏洞。 在这种情况下，AKS 将所有受影响的群集，如果可能，请解决问题的修补程序或向用户提供的升级指南。

辅助角色节点受到此类安全漏洞，如果此问题的零停机时间修补程序可用，AKS 团队会应用该修补程序并通知用户更改。

如果安全修补程序需要辅助角色节点重启，Microsoft 将通知此要求的客户和客户负责执行重新启动或更新，以获取其群集的修补程序。 如果用户不适用的修补程序 AKS 的指导，其群集将继续受到个问题。

### <a name="node-maintenance-and-access"></a>节点维护和访问权限

因为辅助角色节点是共担责任，客户可以根据客户的所有权，登录到这些工作线程并执行可能有害的更改，例如内核更新，删除包和安装新的包。

如果客户执行破坏性操作或触发的操作控制平面组件脱机或否则变得无法正常工作，AKS 服务将检测这种失败，并执行自动更新还原到以前的工作的辅助角色节点状态。

不过，客户可以登录到并更改辅助角色节点，它是*建议您不要*因为这会使你的群集不支持。

## <a name="network-ports-access-and-network-security-groups"></a>网络端口、 访问和网络安全组

作为托管服务，AKS 有特定的网络和连接要求。 这些要求是比正常 IaaS 组件不太灵活。 其他 IaaS 与组件不同，某些操作 （如网络安全组规则，阻止特定端口、 URL 列入白名单和等等的自定义项） 可以使群集不支持 （例如，阻止出站端口 443 的防火墙规则）。

> [!NOTE]
> 完全锁定 （例如，每个端口显式域列入白名单） 出你的群集不受支持的 AKS 方案这一次。 Url 和端口的列表可能会发生更改而不发出警告，并可以由 Azure 支持票证通过提供。 提供的列表仅适用于愿意接受的客户*在任何时候，可能会影响群集的可用性。*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Alpha/Beta Kubernetes 功能 （不支持）

AKS 仅支持在上游 Kubernetes 项目的稳定功能。 除非否则为进行通信，并记录不支持 alpha/Beta 功能在上游 Kubernetes 中可用。

有两种情况下，其中 Alpha 或 Beta 功能可能会推出 GA 之前：

* 客户与 AKS 产品、 支持或工程团队已满足，并且已显式要求试用这些新功能。
* 这些功能已被[通过一个功能标志来启用][ 2] （显式参加）

## <a name="preview-features--feature-flags"></a>预览功能/功能标志

有关功能和所需扩展的测试，社区和用户反馈的功能，Microsoft 将发布新的预览功能或在功能标志后面的功能。 这些功能应被视为预发行版/beta 版，并公开，以使用户有机会试用这些新功能。

但是，这些预览/功能并不用于生产环境中使用 – Api、 行为更改、 bug 修复和其他更改可以进行的功能标志可能会导致不稳定的群集和停机时间。 对这些功能的支持仅限于 bug/问题报告。 不要启用这些功能在生产系统或订阅上。

> [!NOTE]
> 启用预览功能将在 Azure 的生效**订阅**级别。 不要生产订阅上安装预览功能，因为它可以更改默认 API 行为会影响正常操作。

## <a name="upstream-bugs-and-issues"></a>上游 bug 和问题

上游 Kubernetes 项目中提供的开发速度，有一定不能修补或工作-围绕 AKS 系统中，转而要求上游的项目 （如 Kubernetes、 / 辅助角色节点的操作系统和内核） 到更大的修补程序的 bug。 对于我们拥有 （如 Azure 云提供商） 的组件，AKS/Azure 人员将提交到社区中解决此问题上游。

有关技术支持问题的一个或多个上游的 bug 的根本原因的情况下，AKS 支持和工程将执行以下各项：

* 确定并链接任何支持的详细信息，有关为什么这会影响群集和/或工作负荷的上游 bug。 若要观看的问题，请参阅新的 Kubernetes/其他版本时将包括修复所需存储库/问题的链接将提供的客户
* 可能的解决方法或缓解措施：在某些情况下，可能要暂时解决问题 – 在这种情况下，"[已知问题](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)"介绍了在 AKS 存储库上被提交：
  * 问题，并链接到上游 bug
  * 解决方法，然后升级/其他暂留的解决方案的详细信息
  * 包含基于上游发布节奏的大致时间线

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
