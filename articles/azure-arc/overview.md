---
title: Azure Arc 概述
description: 了解什么是 Azure Arc，以及它如何帮助客户使用其他 Azure 服务和功能对混合资源进行管理和治理。
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 8e48378e5032768b3f56a5d99c1189e282ff37f8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714149"
---
# <a name="azure-arc-overview"></a>Azure Arc 概述

如今，各公司都在努力控制和治理越来越复杂的环境。 这些环境跨数据中心、多个云和边缘扩展。 每个环境和云都具有自己的一组不相互连接的管理工具，你需要学习和操作这些工具。

新的 DevOps 和 ITOps 操作模型很难并行实现，因为现有工具无法为新的云本机模式提供支持。

Azure Arc 通过提供一致的多云和本地管理平台，从而简化了治理和管理。 使用 Azure Arc，可以通过将现有资源投影到 Azure 资源管理器中，使用单一虚拟管理平台来管理整个环境。 现在可以管理虚拟机、Kubernetes 群集和数据库，就像它们在 Azure 中运行一样。 不管它们在何处，你都可以使用熟悉的 Azure 服务和管理功能。 使用 Azure Arc，可以继续使用传统的 ITOps，同时引入 DevOps 做法，以支持环境中的新云本机模式。

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Azure Arc 管理控制平面关系图" border="false":::

现在，可以通过 Azure Arc 管理 Azure 外部托管的以下资源类型：

* 服务器 - 运行 Windows 或 Linux 的物理计算机和虚拟机。
* Kubernetes 群集 - 支持多个 Kubernetes 分发。
* Azure 数据服务 - Azure SQL 数据库和 PostgreSQL 超大规模服务。

## <a name="what-does-azure-arc-deliver"></a>Azure Arc 提供哪些功能？

Azure Arc 的主要功能包括：

* 在整个环境中为服务器实现一致的清单、管理、治理和安全性。

* 配置 [Azure VM 扩展](./servers/manage-vm-extensions.md)以使用 Azure 管理服务监视、保护和更新服务器。

* 大规模管理和治理 Kubernetes 群集。

* 使用基于 GitOps 的配置作为代码管理，直接从源代码管理（例如 GitHub）跨一个或多个群集部署应用程序和配置。

* 使用 Azure Policy 实现 Kubernetes 群集的零接触合规性和配置。

* 在任何 Kubernetes 环境（特别是 Azure SQL 托管实例和 Azure Database for PostgreSQL 超大规模）上运行 Azure 数据服务，其中包含升级/更新、安全性和监视等权益，就像在 Azure 中运行一样。 利用弹性缩放，应用更新，而不会导致任何应用程序停机，即使没有持续连接到 Azure。

* 无论使用的是 Azure 门户、Azure CLI、Azure PowerShell 还是 Azure REST API，都可以查看已启用 Azure Arc 的资源的统一体验。

## <a name="how-much-does-azure-arc-cost"></a>Azure Arc 的费用是多少？

下面是 Azure Arc 目前可用功能的定价详细信息。

### <a name="arc-enabled-servers"></a>已启用 Arc 的服务器

Azure Arc 控制平面功能无需额外付费。这包括：

* 通过 Azure 管理组和标记来整理资源。

* 通过 Azure Resource Graph 进行搜索和索引。

* 通过 RBAC 和订阅实现访问和安全性。

* 通过模板和扩展实现环境和自动化。

* 更新管理

在已启用 Arc 的服务器上使用的任何 Azure 服务（例如 Azure 安全中心或 Azure Monitor）都将按照该服务的定价收费。 有关详细信息，请参阅 [Azure 定价页](https://azure.microsoft.com/pricing/)。

### <a name="azure-arc-enabled-kubernetes"></a>已启用 Azure Arc 的 Kubernetes

在当前预览阶段，已启用 Azure Arc 的 Kubernetes 无需额外付费。

### <a name="azure-arc-enabled-data-services"></a>已启用 Azure Arc 的数据服务

在当前预览阶段，已启用 Azure Arc 的数据服务无需额外付费。

## <a name="next-steps"></a>后续步骤

* 若要了解有关已启用 Arc 的服务器的详细信息，请参阅以下[概述](./servers/overview.md)

* 若要了解有关已启用 Arc 的 Kubernetes 的详细信息，请参阅以下[概述](./kubernetes/overview.md)

* 若要了解有关已启用 Arc 的数据服务的详细信息，请参阅以下[概述](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)
