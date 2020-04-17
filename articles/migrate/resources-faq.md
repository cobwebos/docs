---
title: Azure 迁移常见问题解答
description: 获取有关 Azure 迁移服务的常见问题的解答。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530311"
---
# <a name="azure-migrate-common-questions"></a>Azure 迁移：常见问题

本文回答了有关 Azure 迁移的常见问题。 如果在阅读本文后有疑问，可以在[Azure 迁移论坛](https://aka.ms/AzureMigrateForum)中发布这些问题。 您还可以查看以下文章：

- 有关[Azure 迁移设备的问题](common-questions-appliance.md)
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题

## <a name="what-is-azure-migrate"></a>Azure Migrate 是什么？

Azure 迁移提供了一个中心中心，用于跟踪本地应用和工作负荷以及私有和公共云 VM 到 Azure 的发现、评估和迁移。 中心提供用于评估和迁移以及第三方 ISV 服务的 Azure 迁移工具。 [了解详细信息](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>使用 Azure 迁移可以执行什么操作？

使用 Azure 迁移可发现、评估和将本地基础结构、应用程序和数据迁移到 Azure。 Azure 迁移支持本地 VMware VM、超虚拟机、物理服务器、其他虚拟化 VM、数据库、Web 应用和虚拟桌面的评估和迁移。 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure 迁移和 Azure 站点恢复之间的区别是什么？

[Azure 迁移](migrate-services-overview.md)提供了一个集中式中心，用于评估和迁移到 Azure。 

[Azure 站点恢复](../site-recovery/site-recovery-overview.md)是灾难恢复解决方案。 

Azure 迁移：服务器迁移工具使用某些后端站点恢复功能来提升和转移某些本地计算机。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure 迁移：服务器评估和 MAP 工具包之间的区别是什么？

服务器评估提供评估，以帮助迁移就绪，并评估迁移到 Azure 的工作负载。 [Microsoft 评估和规划 （MAP） 工具包](https://www.microsoft.com/download/details.aspx?id=7826)可帮助完成其他任务，包括较新版本的 Windows 客户端和服务器操作系统的迁移规划以及软件使用情况跟踪。 对于这些方案，请继续使用 MAP 工具包。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>服务器评估和站点恢复部署规划器之间的区别是什么？

服务器评估是一个迁移规划工具。 站点恢复部署规划器是灾难恢复规划工具。

根据您要执行的操作选择工具：

- **计划本地迁移到 Azure：** 如果计划将本地服务器迁移到 Azure，请使用服务器评估进行迁移规划。 服务器评估评估本地工作负载，并提供帮助您迁移的指导和工具。 迁移计划实施后，可以使用 Azure 迁移：服务器迁移等工具将计算机迁移到 Azure。
- **将灾难恢复计划到 Azure：** 如果计划通过站点恢复从本地设置为 Azure，请使用站点恢复部署规划器。 部署规划器针对本地环境提供针对站点恢复的深入、特定于站点的评估，以便进行灾难恢复。 它提供与灾难恢复相关的建议，如复制和故障转移。

## <a name="how-does-server-migration-work-with-site-recovery"></a>服务器迁移如何处理站点恢复？

- 如果使用 Azure 迁移：服务器迁移来执行本地 VMware VM 的无*代理*迁移，则迁移是 Azure 迁移的本机迁移，并且不使用站点恢复。
- 如果使用 Azure 迁移：服务器迁移来执行*基于代理*的 VMware VM 迁移，或者迁移 Hyper-VM 或物理服务器，则 Azure 迁移：服务器迁移使用 Azure 站点恢复复制引擎。

## <a name="which-geographies-are-supported"></a>支持哪些地理位置？

查看[公共](migrate-support-matrix.md#supported-geographies-public-cloud)和政府[云](migrate-support-matrix.md#supported-geographies-azure-government)的支持地理位置。

## <a name="how-do-i-get-started"></a>如何入门？

确定所需的工具，然后将该工具添加到 Azure 迁移项目。 

要添加 ISV 工具或移动：

1. 开始获得许可证，或注册免费试用版，根据工具策略。 ISV 工具的许可符合 ISV 或工具的许可模型。
2. 每个工具中都有一个用于连接到 Azure Migrate 的选项。 按照工具说明和文档将工具与 Azure 迁移连接。

可以从 Azure 迁移项目中、跨 Azure 和其他工具跟踪迁移过程。

## <a name="how-do-i-delete-a-project"></a>如何删除项目？

了解如何[删除项目](how-to-delete-project.md)。 

## <a name="next-steps"></a>后续步骤

阅读[Azure 迁移概述](migrate-services-overview.md)。
