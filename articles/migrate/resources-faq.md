---
title: 有关 Azure Migrate 的常见问题
description: 获取有关 Azure Migrate 服务的常见问题解答。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: a9d008406e59c66a8b742194e2e2d388c778ac9e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989342"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate：常见问题

本文解答了有关 Azure Migrate 的常见问题。 阅读本文后，如果你有更多的查询，请将其发布到[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)。 如果你有其他问题，请查看以下文章：

- 关于 Azure Migrate 设备的[问题](common-questions-appliance.md)。
- 有关发现、评估和依赖项可视化的[问题](common-questions-discovery-assessment.md)。


## <a name="what-is-azure-migrate"></a>Azure Migrate 是什么？

Azure Migrate 提供了一个中心，用于跟踪对 Azure 的本地应用程序、工作负荷以及专用/公有云 Vm 的发现、评估和迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。 [了解详细信息](migrate-services-overview.md)。


## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate 可以执行哪些操作？

使用 Azure Migrate 发现、评估和将本地基础结构、应用程序和数据迁移到 Azure。 Azure Migrate 支持评估和迁移本地 VMware Vm、Hyper-v Vm、物理服务器、其他虚拟化 Vm、数据库、web 应用和虚拟桌面。 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Azure Migrate 和 Site Recovery 之间的区别是什么？

Azure Migrate 提供了一个集中式中心，用于评估和迁移到 Azure。 [Azure Site Recovery](../site-recovery/site-recovery-overview.md)是一种灾难恢复解决方案。 Azure Migrate：服务器迁移工具使用一些后端 Site Recovery 功能来迁移某些本地计算机。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate Server 评估和地图工具包之间的区别是什么？

服务器评估提供评估以帮助迁移准备情况，并评估要迁移到 Azure 的工作负荷。 [Microsoft 评估和计划（MAP）工具包](https://www.microsoft.com/download/details.aspx?id=7826)可帮助执行其他任务，包括针对较新版本的 Windows 客户端/服务器操作系统和软件使用跟踪的迁移规划。 对于这些方案，请继续使用地图工具包。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>服务器评估与 Site Recovery 部署规划器之间的区别是什么？

服务器评估是一种迁移规划工具。 Site Recovery 部署规划器是一种灾难恢复规划工具。

- **规划本地迁移到 azure**：如果计划将本地服务器迁移到 azure，请使用服务器评估进行迁移规划。 它会评估本地工作负荷，并提供指导和工具来帮助你进行迁移。 迁移计划准备就绪后，可以使用工具（包括 Azure Migrate 服务器迁移）将计算机迁移到 Azure。
- **规划 azure 到 azure 的灾难恢复**：如果计划使用 Site Recovery 设置从本地到 Azure 的灾难恢复，请使用 Site Recovery 部署规划器。 部署规划器提供针对灾难恢复目的的本地环境的深入、Site Recovery 特定的评估。 它提供有关灾难恢复的建议，例如复制和故障转移。

## <a name="how-does-server-migration-work-with-site-recovery"></a>服务器迁移如何与 Site Recovery 配合工作？

- 如果使用 Azure Migrate：服务器迁移执行本地 VMware Vm 的无代理迁移，则迁移是 Azure Migrate 的本机迁移，Site Recovery 不使用。
- 如果使用 Azure Migrate：服务器迁移执行 VMware Vm 的基于代理的迁移，或者迁移 Hyper-v Vm 或物理服务器，则 Azure Migrate 服务器迁移使用 Azure Site Recovery 复制引擎。


## <a name="which-geographies-are-supported"></a>支持哪些地区？

查看适用于[VMWARE vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)和[hyper-v vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)的 Azure Migrate 支持的地理位置。

## <a name="how-do-i-get-started"></a>我如何开始？

确定所需的工具，并将其添加到 Azure Migrate 项目。 如果添加 ISV 工具或 Movere：
- 根据工具策略获取许可证或注册免费试用版即可开始使用该工具。 ISV 工具的许可符合 ISV 或工具的许可模型。
- 每个工具中都有一个用于连接到 Azure Migrate 的选项。 按照工具说明和文档操作，将该工具与 Azure Migrate 连接起来。
可以在 Azure Migrate 项目中跨 Azure 和其他工具集中跟踪迁移历程。

## <a name="how-do-i-delete-a-project"></a>如何实现删除项目？

[了解如何](how-to-delete-project.md)删除项目。 


## <a name="do-machines-retain-domain-identity-after-migration"></a>迁移后是否保留域标识？

假定域服务已[集成到 Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/)中，则迁移后会保留域中的域成员身份和计算机标识。 

## <a name="next-steps"></a>后续步骤
阅读[Azure Migrate 概述](migrate-services-overview.md)。
