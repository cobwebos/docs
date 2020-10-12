---
title: 通过 Azure Arc 大规模管理混合基础结构
description: 了解如何在 Azure 外部有效管理客户的计算机和 Kubernetes 群集。
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 66a798265683045d7ff9f3d8d811141800d08f9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336609"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>通过 Azure Arc 大规模管理混合基础结构

作为服务提供商，你可能已将多个客户租户载入 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 允许服务提供商同时在多个 Azure Active Directory (Azure AD) 租户执行大规模操作，使管理任务更高效。

[Azure Arc](../../azure-arc/overview.md) 有助于跨本地、边缘和多云简化复杂的分布式环境，从而可以在任何位置部署 azure 服务并将 azure 管理扩展到任何基础结构。

通过 [启用了 Azure Arc 的服务器](../../azure-arc/servers/overview.md)，客户可以管理在其企业网络中托管的任何 Windows 和 Linux 计算机，其方式与管理本机 Azure 虚拟机的方式相同。 通过将混合计算机链接到 Azure，它就会连接到 Azure 并被视为 Azure 中的资源。 然后，服务提供商可以将这些非 Azure 计算机及其客户的 Azure 资源进行管理。

[启用了 Azure Arc Kubernetes (预览版) ](../../azure-arc/kubernetes/overview.md) 使客户能够在 Azure 内部或外部附加和配置 Kubernetes 群集。 将 Kubernetes 群集附加到 Azure Arc 后，它将显示在 Azure 门户中，其中包含 Azure 资源管理器 ID 和托管标识。 群集附加到标准 Azure 订阅，位于资源组中，可以像任何其他 Azure 资源一样接收标记。

本主题概述了服务提供商如何使用启用了 Azure Arc 的服务器和启用了 Azure Arc 的 Kubernetes (预览) 以一种可缩放的方式管理其客户的混合环境，并可跨所有托管客户租户进行查看。

> [!TIP]
> 尽管我们指的是本主题中的服务提供商和客户，但本指南也适用于 [使用 Azure Lighthouse 管理多个租户的企业](../concepts/enterprise.md)。

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>启用启用了 Azure Arc 的服务器时大规模管理混合服务器

作为服务提供商，你可以使用 [Azure 连接的计算机代理](../../azure-arc/servers/agent-overview.md)管理 azure 外部的本地 Windows Server 或 Linux 计算机。

在 Azure 门户中查看委派订阅的资源时，会看到这些连接的计算机标记为 " **Azure Arc**"。你可以使用 Azure 构造（如 Azure 策略和标记）管理这些连接的计算机，与管理客户的 Azure 资源的方式相同。 你还可以跨客户租户工作，同时管理所有连接的混合计算机。

例如，你可以 [确保在客户的混合计算机上应用相同的一组策略](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md)。 你还可以使用 Azure 安全中心来监视所有客户混合环境的符合性，或 [使用 Azure Monitor 将数据直接从混合计算机收集](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) 到 Log Analytics 工作区。 可将[虚拟机扩展](../../azure-arc/servers/manage-vm-extensions.md)部署到非 Azure Windows 和 Linux vm，简化客户混合计算机的管理。

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes-preview"></a>通过启用了 Azure Arc 的 Kubernetes 管理混合 Kubernetes 群集 (预览) 

> [!NOTE]
> 启用 Azure Arc 的 Kubernetes 目前为预览版。 目前，我们不建议将其用于生产工作负荷。

你可以 [使用 Azure Arc 管理已连接到客户的订阅](../../azure-arc/kubernetes/connect-cluster.md)的 Kubernetes 群集，就像它们是在 azure 中运行一样。

如果你的客户创建了 [服务主体帐户以将 Kubernetes 群集加入 Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md)，则可以访问此服务主体帐户来载入和管理群集。 当包含服务主体帐户的订阅已 [载入到 Azure Lighthouse](onboard-customer.md)时，管理租户中的用户可以完成此操作，这些用户已被授予 "Kubernetes 群集-Azure Arc 载入" Azure 内置角色。

可以使用 GitOps 为连接的群集部署 [配置](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) 和 [Helm 图表](../../azure-arc/kubernetes/use-gitops-with-helm.md) 。

你还可以使用 Azure Monitor 监视连接的群集，并 [使用 Azure 策略大规模应用群集配置](../../azure-arc/kubernetes/use-azure-policy.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Arc GitHub 存储库](https://github.com/microsoft/azure_arc)中的 jumpstarts 和示例。 
- 了解 [支持 Azure Arc 的服务器的支持方案](../../azure-arc/servers/overview.md#supported-scenarios)。
- 了解 [Azure Arc 支持的 Kubernetes 分布](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions)。
- 了解如何 [大规模部署策略](policy-at-scale.md)。
- 了解如何 [使用大规模 Azure Monitor 日志](monitor-at-scale.md)。

