---
title: Azure Migrate 常见问题
description: 获取有关 Azure Migrate 服务的常见问题解答。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 95fd2ea9ecf5e4bcdf3fb2291e4cf7bf5b111c5f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847459"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate：常见问题

本文解答了有关 Azure Migrate 的常见问题。 如果在阅读本文后有任何问题，可以在[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中发布。 你还可以查看以下文章：

- 关于[Azure Migrate 设备](common-questions-appliance.md)的问题
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题

## <a name="what-is-azure-migrate"></a>什么是 Azure Migrate？

Azure Migrate 提供了一个中心，用于跟踪发现、评估和将本地应用、工作负荷和私有云 Vm 迁移到 Azure。 该中心提供用于评估和迁移的 Azure Migrate 工具以及第三方 ISV 产品。 [了解详细信息](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate 可以执行哪些操作？

使用 Azure Migrate 发现、评估和将本地基础结构、应用程序和数据迁移到 Azure。 Azure Migrate 支持评估和迁移本地 VMware Vm、Hyper-v Vm、物理服务器、其他虚拟化 Vm、数据库、web 应用和虚拟桌面。 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate 和 Azure Site Recovery 之间的区别是什么？

[Azure Migrate](migrate-services-overview.md)提供了一个集中式中心，用于评估和迁移到 Azure。 

- 使用 Azure Migrate 可通过 Azure Migrate 工具、其他 Azure 服务和第三方工具提供互操作性和未来的扩展性。
- Azure Migrate：服务器迁移工具专用于将服务器迁移到 Azure。 它针对迁移进行了优化。 不需要了解与迁移无关的概念和方案。 
- 从 VM 开始复制开始，180天内没有用于迁移的工具使用量。 这为你提供了完成迁移的时间。 只需为复制中使用的存储和网络资源付费，并为测试迁移期间使用的计算费用付费。
- Azure Migrate 支持 Site Recovery 支持的所有迁移方案。 此外，对于 VMware Vm，Azure Migrate 提供无代理迁移选项。
- 我们仅确定 Azure Migrate： Server 迁移工具的新迁移功能的优先级。 这些功能并不针对 Site Recovery。

[Azure Site Recovery](../site-recovery/site-recovery-overview.md)应仅用于灾难恢复。

Azure Migrate：服务器迁移工具使用一些后端 Site Recovery 功能来迁移某些本地计算机的迁移。

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>我有一个项目，该项目具有以前的经典体验 Azure Migrate。 如何实现开始使用新版本吗？

无法将以前版本中的项目或组件升级到新版本。 需要[创建新的 Azure Migrate 项目](create-manage-projects.md)并向其[添加评估和迁移工具](how-to-add-tool-first-time.md)。 学习教程，了解如何使用可用的评估和迁移工具。 如果已将 Log Analytics 工作区附加到了经典项目，则可以在删除经典项目后将其附加到当前版本的项目。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate：服务器评估和地图工具包之间的区别是什么？

服务器评估提供评估以帮助迁移准备情况，并评估要迁移到 Azure 的工作负荷。 [Microsoft 评估和计划 (MAP) 工具包](https://www.microsoft.com/download/details.aspx?id=7826)可帮助执行其他任务，包括针对较新版本的 Windows 客户端和服务器操作系统以及软件使用跟踪的迁移规划。 对于这些方案，请继续使用地图工具包。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>服务器评估与 Site Recovery 部署规划器之间的区别是什么？

服务器评估是一种迁移规划工具。 Site Recovery 部署规划器是一种灾难恢复规划工具。

根据要执行的操作选择工具：

- **规划本地迁移到 azure**：如果计划将本地服务器迁移到 azure，请使用服务器评估进行迁移规划。 服务器评估会评估本地工作负荷，并提供指导和工具来帮助你进行迁移。 迁移计划准备就绪后，可以使用 Azure Migrate 的工具：服务器迁移将计算机迁移到 Azure。
- **规划 azure 到 azure 的灾难恢复**：如果计划使用 Site Recovery 设置从本地到 Azure 的灾难恢复，请使用 Site Recovery 部署规划器。 部署规划器提供针对灾难恢复目的的本地环境的深入、Site Recovery 特定的评估。 它提供与灾难恢复相关的建议，例如复制和故障转移。

## <a name="how-does-server-migration-work-with-site-recovery"></a>服务器迁移如何与 Site Recovery 配合工作？

- 如果你使用 Azure Migrate：服务器迁移来执行本地 VMware Vm 的*无代理*迁移，则迁移是本机到 Azure Migrate 并且 Site Recovery 不使用。
- 如果使用 Azure Migrate：服务器迁移执行 VMware Vm 的*基于代理*的迁移，或者迁移 hyper-v vm 或物理服务器，Azure Migrate：服务器迁移使用 Azure Site Recovery 复制引擎。

## <a name="which-geographies-are-supported"></a>支持哪些地区？

查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

## <a name="how-do-i-get-started"></a>如何开始？

确定所需的工具，然后将该工具添加到 Azure Migrate 项目。 

添加 ISV 工具或 Movere：

1. 首先获取许可证，或根据工具策略注册免费试用版。 ISV 工具的许可符合 ISV 或工具的许可模型。
2. 每个工具中都有一个用于连接到 Azure Migrate 的选项。 按照工具说明和文档，将该工具与 Azure Migrate 连接起来。

你可以从 Azure Migrate 项目、在 Azure 和其他工具中跟踪迁移旅程。

## <a name="how-do-i-delete-a-project"></a>如何实现删除项目？

了解如何[删除项目](how-to-delete-project.md)。 

## <a name="next-steps"></a>后续步骤

阅读[Azure Migrate 概述](migrate-services-overview.md)。
