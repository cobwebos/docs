---
title: 关于 Azure Migrate
description: 了解如何使用 Azure Migrate 服务评估和迁移服务器。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5843e6a5669cd90cdc0572e7a104c4fdf70ac2b3
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669131"
---
# <a name="about-azure-migrate"></a>关于 Azure Migrate

本文简要概述了 Azure Migrate。

Azure Migrate 有助于将企业从本地迁移到 Azure。 Azure Migrate 提供了一个中心，用于跟踪 Azure 的本地基础结构、应用程序和数据的发现、评估以及迁移。  Azure Migrate 提供：

- **统一迁移平台**：用于启动、运行和跟踪 Azure 迁移进程的单一门户。
- **工具范围**：中心提供 Azure Migrate 工具评估和迁移，并与其他 Azure 服务以及其他工具和独立软件供应商 (ISV) 产品/服务相集成。
- **工作负荷**：Azure Migrate 提供评估和迁移用于：
    - **服务器**：使用 Azure Migrate 服务器评估、Azure Migrate 服务器迁移和其他工具来评估服务器并将其迁移到 Azure VM。
    - **数据库**：利用 Microsoft 和 ISV 工具评估本地数据库并将其迁移到 Azure SQL DB 或 Azure SQL 托管实例。
    - **Web 应用程序**：使用 Azure 应用服务助手评估本地 Web 应用程序并将其迁移到 Azure 应用服务。
    - **虚拟桌面**：使用 ISV 工具评估本地虚拟桌面基础结构 (VDI) 并将其迁移到 Azure 中的 Windows 虚拟桌面。
    - **Data**：使用 Azure Data Box 系列产品快速、经济、高效地将大量数据迁移到 Azure。

## <a name="azure-migrate-versions"></a>Azure Migrate 版本

Azure Migrate 服务当前有两个版本：

- **当前版本**：使用此版本创建 Azure Migrate 项目，发现本地计算机，并安排评估和迁移。 [详细了解](whats-new.md)此版本中的新增功能。
- **以前版本**：如果使用的是以前的 Azure Migrate 版本（仅支持评估本地 VMware VM），则现在应使用当前版本。 你无法再使用以前版本创建 Azure Migrate 项目，建议不要执行新的发现。 若要访问现有项目，请在 Azure 门户中选择“所有服务”，然后搜索 Azure Migrate   。 Azure Migrate 仪表板上提供了一个通知和一个用于访问旧 Azure Migrate 项目的链接。



## <a name="isv-integration"></a>ISV 集成

除本机 Azure 工具外，Azure Migrate 还与大量 ISV 产品/服务集成。 

**ISV** | **功能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | 迁移服务器
[Cloudamize](https://www.cloudamize.com/platform) | 评估服务器
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | 评估和迁移服务器
[设备 42](https://docs.device42.com/) | 评估服务器
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 评估 VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 迁移服务器
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | 评估服务器
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | 评估服务器和数据库

## <a name="azure-tool-integration"></a>Azure 工具集成

下表汇总了 Azure Migrate 中集成的其他工具。

**工具** | **详细信息**
--- | ---
“Azure Migrate:服务器评估”评估 VMware VM | 评估服务器
“Azure Migrate:Server 迁移 | 迁移服务器
数据迁移助手 (DMA) | 评估数据库
数据库迁移服务 (DMS) | 迁移数据库
Movere | 评估服务器
Web 应用迁移助手 | 评估和迁移 Web 应用



### <a name="selecting-a-tool"></a>选择工具

确定所需的工具，并将其添加到 Azure Migrate 项目。

- 如果添加 ISV 工具或 Movere：
    - 根据工具策略获取许可证或注册免费试用版即可开始使用该工具。 ISV 工具的许可符合 ISV 或工具的许可模型。
    - 每个工具中都有一个用于连接到 Azure Migrate 的选项。 按照工具说明和文档操作，将该工具与 Azure Migrate 连接起来。
- 可以在 Azure Migrate 项目中跨 Azure 和其他工具集中跟踪迁移历程。



## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate 服务器评估工具

“Azure Migrate:服务器评估工具可发现并评估本地 VMware VM、Hyper-V VM 和物理服务器，以便迁移到 Azure。 它有助于识别以下内容:

- **Azure 迁移就绪性：** 评估本地计算机是否已准备好迁移到 Azure。
- **Azure 大小调整：** 迁移后 Azure VM 的估计大小。
- **Azure 成本估算：** 在 Azure 中运行本地服务器的估计成本。
- **依赖关系可视化：** 跨服务器依赖关系（如果启用了依赖关系可视化），以及将从属服务器迁移到 Azure 的最佳方法。

服务器评估使用在本地部署并向服务器评估注册的轻型设备。

- 设备会发现本地计算机。
- 然后连接到服务器评估，并持续向 Azure Migrate 发送计算机元数据和性能数据。
- 设备发现是无代理的。 无需在已发现的计算机上安装任何内容。
- 发现后，可将已发现的计算机收集到组中。 通常会将要迁移的计算机组合在一起。
- 为组创建评估。 随后便可分析评估，以确定迁移策略。

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate 服务器迁移工具

“Azure Migrate:服务器迁移工具有助于将本地 VMware VM、Hyper-V VM、物理服务器、其他虚拟机和公有云 VM 迁移到 Azure。 可在评估计算机后进行迁移，也可不评估直接进行迁移。


## <a name="database-migration-assistant"></a>数据库迁移助手

Azure Migrate 与 Microsoft 数据迁移助手 (DMA) 集成，用于评估本地 SQL Server 数据库以迁移到 Azure SQL DB、Azure SQL 托管实例或运行 SQL Server 的 Azure VM。 DMA 提供有关迁移的潜在障碍问题信息。 它确定不受支持的功能，以及迁移后可从中受益的新功能，并帮助确定数据库迁移的正确路径。 [了解详细信息](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。

## <a name="database-migration-service"></a>数据库迁移服务

Azure Migrate 与 Azure 数据库迁移服务 (DMS) 集成，以将本地数据库迁移到 Azure。 使用 DMS 将本地数据库迁移到运行 SQL 的 Azure VM、Azure SQL DB 和 Azure SQL 托管实例。 [了解详细信息](https://docs.microsoft.com/azure/dms/dms-overview)。

## <a name="movere"></a>Movere

 
Movere 是一个 SaaS 平台，它可以准确呈现整个 IT 环境在一天内的情况，以此增强商业智能。 随着组织的发展、变革和做出数字优化，该解决方案可让企业自信地洞察和控制其环境，而不管使用哪种平台和应用程序，或者在哪个地理位置运营。 Movere 已被 Microsoft [收购](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)，不再作为单独的产品/服务销售。  可以通过 Microsoft 解决方案评估和云经济计划获取 Movere。 [详细了解](https://www.movere.io) Movere。 如果有疑问，请联系 movereq@microsoft.com 或 Microsoft 代表。

此外，我们建议考虑 Azure Migrate - 内置的迁移服务。 Azure Migrate 提供一个中心来简化迁移到云的过程。 该中心为不同的工作负荷（包括物理和虚拟服务器、数据库及应用程序）提供全面的支持。 借助端到端的可见性可以轻松跟踪整个发现、评估和迁移过程的进度。 此外，Azure Migrate 内置了 Azure 与合作伙伴的 ISV 工具，可提供丰富的功能，包括虚拟和物理服务器发现、基于性能的大小调整、成本规划、基于导入的评估，以及无代理应用程序依赖关系分析。 如果你希望通过专家来帮助自己入门，Microsoft 训练有素的 [Azure 专家托管服务提供商](https://azure.microsoft.com/partners)可引导你完成整个历程。 请查看 [Azure Migrate 网站](https://azure.microsoft.com/services/azure-migrate/)。 
 

## <a name="web-app-migration-assistant"></a>Web 应用迁移助手

Azure Migrate 与 Azure 应用服务迁移助手集成。 从 Azure Migrate 中心，可以按如下所示使用助手评估本地 Web 应用并将其迁移到 Azure：

- **联机评估 Web 应用**：使用 Azure 应用服务迁移助手评估用于迁移到 Azure 应用服务的本地网站。
- **迁移 Web 应用**：使用 Azure 应用服务迁移助手将 .NET 和 PHP Web 应用迁移到 Azure。

[详细了解](https://appmigration.microsoft.com/)助手。



## <a name="offline-data-migration"></a>脱机数据迁移

可使用 Azure Data Box 产品将大量数据脱机迁移到 Azure。 [了解详细信息](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>后续步骤

- 试用我们的教程来评估 [VMware VM](tutorial-assess-vmware.md) 和 [Hyper-V VM](tutorial-assess-hyper-v.md)。
- [详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。
- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。
