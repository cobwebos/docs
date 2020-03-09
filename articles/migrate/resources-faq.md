---
title: Azure Migrate 常见问题
description: 获取有关 Azure Migrate 服务的常见问题解答。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: c85c5c6e11beb0178139dad152f56f420b2ac26f
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926732"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate：常见问题

本文解答了有关 Azure Migrate 的常见问题。 如果在阅读本文后有任何问题，可以在[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中发布。 你还可以查看以下文章：

- 关于[Azure Migrate 设备](common-questions-appliance.md)的问题
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题

## <a name="what-is-azure-migrate"></a>Azure Migrate 是什么？

Azure Migrate 提供了一个中心，用于跟踪发现、评估和将本地应用、工作负荷和私有云 Vm 迁移到 Azure。 该中心提供用于评估和迁移的 Azure Migrate 工具以及第三方 ISV 产品。 [了解详细信息](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate 可以执行哪些操作？

使用 Azure Migrate 发现、评估和将本地基础结构、应用程序和数据迁移到 Azure。 Azure Migrate 支持评估和迁移本地 VMware Vm、Hyper-v Vm、物理服务器、其他虚拟化 Vm、数据库、web 应用和虚拟桌面。 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate 和 Azure Site Recovery 之间的区别是什么？

[Azure Migrate](migrate-services-overview.md)提供了一个集中式中心，用于评估和迁移到 Azure。 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md)是一种灾难恢复解决方案。 

Azure Migrate：服务器迁移工具使用一些后端 Site Recovery 功能来迁移某些本地计算机的迁移。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate：服务器评估和地图工具包之间的区别是什么？

服务器评估提供评估以帮助迁移准备情况，并评估要迁移到 Azure 的工作负荷。 [Microsoft 评估和计划（MAP）工具包](https://www.microsoft.com/download/details.aspx?id=7826)可帮助执行其他任务，包括针对较新版本的 Windows 客户端和服务器操作系统以及软件使用跟踪的迁移规划。 对于这些方案，请继续使用地图工具包。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>服务器评估与 Site Recovery 部署规划器之间的区别是什么？

服务器评估是一种迁移规划工具。 Site Recovery 部署规划器是一种灾难恢复规划工具。

根据要执行的操作选择工具：

- **规划本地迁移到 azure**：如果计划将本地服务器迁移到 azure，请使用服务器评估进行迁移规划。 服务器评估会评估本地工作负荷，并提供指导和工具来帮助你进行迁移。 迁移计划准备就绪后，可以使用 Azure Migrate 的工具：服务器迁移将计算机迁移到 Azure。
- **规划 azure 到 azure 的灾难恢复**：如果计划使用 Site Recovery 设置从本地到 Azure 的灾难恢复，请使用 Site Recovery 部署规划器。 部署规划器提供针对灾难恢复目的的本地环境的深入、Site Recovery 特定的评估。 它提供与灾难恢复相关的建议，例如复制和故障转移。

## <a name="how-does-server-migration-work-with-site-recovery"></a>服务器迁移如何与 Site Recovery 配合工作？

- 如果你使用 Azure Migrate：服务器迁移来执行本地 VMware Vm 的*无代理*迁移，则迁移是本机到 Azure Migrate 并且 Site Recovery 不使用。
- 如果使用 Azure Migrate：服务器迁移执行 VMware Vm 的*基于代理*的迁移，或者迁移 hyper-v vm 或物理服务器，Azure Migrate：服务器迁移使用 Azure Site Recovery 复制引擎。

## <a name="which-geographies-are-supported"></a>支持哪些地区？

- **Vmware vm**：查看 vmware vm[支持的 Azure Migrate 地理区域](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)。
- **Hyper-v vm**：查看适用于 hyper-v vm 的 Azure Migrate[支持的地理](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)位置。

## <a name="how-do-i-get-started"></a>如何入门？

确定所需的工具，然后将该工具添加到 Azure Migrate 项目。 

添加 ISV 工具或 Movere：

1. 首先获取许可证，或根据工具策略注册免费试用版。 ISV 工具的许可符合 ISV 或工具的许可模型。
2. 每个工具中都有一个用于连接到 Azure Migrate 的选项。 按照工具说明和文档，将该工具与 Azure Migrate 连接起来。

你可以从 Azure Migrate 项目、在 Azure 和其他工具中跟踪迁移旅程。

## <a name="how-do-i-delete-a-project"></a>如何实现删除项目？

了解如何[删除项目](how-to-delete-project.md)。 

## <a name="next-steps"></a>后续步骤

阅读[Azure Migrate 概述](migrate-services-overview.md)。
