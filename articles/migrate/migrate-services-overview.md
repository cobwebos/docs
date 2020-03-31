---
title: 关于 Azure Migrate
description: 了解 Azure Migrate 服务。
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: a9723f15d496393d27bdd227ec1121a7878b37e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127745"
---
# <a name="about-azure-migrate"></a>关于 Azure Migrate

本文简要概述了 Azure Migrate 服务。

Azure Migrate 提供一个中心用于评估本地服务器、基础结构、应用程序和数据并将其迁移到 Azure。 Azure Migrate 提供以下功能：

- **统一迁移平台**：用于启动、运行和跟踪 Azure 迁移进程的单一门户。
- **工具范围**：用于评估和迁移的一系列工具。 工具包括“Azure Migrate: 服务器评估”和“Azure Migrate:服务器迁移”。 Azure Migrate 与其他 Azure 服务集成，并与其他工具和独立软件供应商 (ISV) 产品/服务集成。
- **评估和迁移**：在 Azure Migrate 中心，可以评估和迁移：
    - **服务器**：评估本地服务器并将其迁移到 Azure VM。
    - **数据库**：评估本地数据库，并将其迁移到 Azure SQL DB 或 Azure SQL 托管实例。
    - **Web 应用程序**：使用 Azure 应用服务助手评估本地 Web 应用程序并将其迁移到 Azure 应用服务。
    - **虚拟桌面**：评估本地虚拟桌面基础结构 (VDI) 并将其迁移到 Azure 中的 Windows 虚拟桌面。
    - **Data**：使用 Azure Data Box 产品快速、经济、高效地将大量数据迁移到 Azure。 


## <a name="integrated-tools"></a>集成工具

Azure Migrate 中心包含以下工具。

**工具** | **评估/迁移** | **详细信息**
--- | --- | ---
**Azure Migrate: 服务器评估** | 评估服务器。 | 发现并评估本地 VMware VM、Hyper-V VM 和物理服务器，以准备好迁移到 Azure。
**Azure Migrate: 服务器迁移** | 迁移服务器。 | 将 VMware VM、Hyper-V VM、物理服务器、其他虚拟机和公有云 VM 迁移到 Azure。 
**数据迁移助手 (DMA)** | 评估要迁移到 Azure SQL 数据库、Azure SQL 托管实例或运行 SQL Server 的 Azure VM 的本地 SQL Server 数据库。 | DMA 有助于查明迁移时的潜在阻碍性问题。 它可以识别不受支持的功能，以及迁移后可从中受益的新功能，并帮助识别数据库迁移的正确路径。 [了解详细信息](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。
**数据库迁移服务 (DMS)** | 将本地数据库迁移到运行 SQL 的 Azure VM、Azure SQL DB 和 Azure SQL 托管实例。 | [详细了解](https://docs.microsoft.com/azure/dms/dms-overview) DMS。
**Movere** | 评估服务器。 | [详细了解](#movere) Movere。
**Web 应用迁移助手** | 评估本地 Web 应用并将其迁移到 Azure。 |  使用 Azure 应用服务迁移助手评估要迁移到 Azure 应用服务的本地网站。<br/><br/> 使用助手将 .NET 和 PHP Web 应用迁移到 Azure。 [详细了解](https://appmigration.microsoft.com/) Azure 应用服务迁移助手。
**Azure Data Box** | 脱机数据迁移。 | 使用 Azure Data Box 产品将大量数据脱机迁移到 Azure。 [了解详细信息](https://docs.microsoft.com/azure/databox/)。

## <a name="isv-integration"></a>ISV 集成

Azure Migrate 还与大量 ISV 产品/服务集成。 

**ISV**    | **功能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | 迁移服务器
[Cloudamize](https://www.cloudamize.com/platform) | 评估服务器
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | 评估和迁移服务器
[设备 42](https://docs.device42.com/) | 评估服务器
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 评估 VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 迁移服务器
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | 评估服务器
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | 评估服务器和数据库


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate 服务器评估工具

“Azure Migrate: 服务器评估”工具可发现并评估要迁移到 Azure 的本地 VMware VM、Hyper-V VM 和物理服务器。 该工具的作用如下：

- **Azure 迁移就绪性：** 评估本地计算机是否已准备好迁移到 Azure。
- **Azure 大小调整：** 估算迁移后的 Azure VM 大小。
- **Azure 成本估算：** 在 Azure 中运行本地服务器的估计成本。
- **依赖关系分析：** 如果将服务器评估与[依赖关系分析](concepts-dependency-visualization.md)结合使用，则可以有效识别服务器之间的依赖关系，并优化将相互依赖的服务器移到 Azure 的策略。


服务器评估使用本地部署的轻型 [Azure Migrate 设备](migrate-appliance.md)。

- 该设备在 VM 或物理服务器上运行。 可以使用下载的模板轻松安装它。
- 该设备发现本地计算机，并持续向 Azure Migrate 发送计算机元数据和性能数据。
- 设备发现是无代理的。 无需在已发现的计算机上安装任何组件。
- 完成设备发现后，可将发现的计算机收集到组中，然后对组运行评估。

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate 服务器迁移工具

“Azure Migrate: 服务迁移”工具可帮助你将本地 VMware VM、Hyper-V VM、物理服务器、其他虚拟机和公有云 VM 迁移到 Azure。 可在评估计算机后进行迁移，也可不评估直接进行迁移。 

对于 VMware VM 的无代理迁移以及 Hyper-V VM 的迁移，服务器迁移将使用本地部署的 Azure Migrate 设备。 如果设置了服务器评估，则也会使用该设备，前一部分提供了相关介绍。


## <a name="selecting-assessmentmigration-tools"></a>选择评估/迁移工具

在 Azure Migrate 中心，选择用于评估或迁移的工具，并将其添加到 Azure Migrate 项目。 如果添加 ISV 工具或 Movere：

- 若要开始使用该工具，请根据工具说明获取许可证或注册免费试用版。 工具许可方式由 ISV 或工具决定。 
- 每个工具中都有一个用于连接到 Azure Migrate 的选项。 按照工具说明进行连接。
- 在 Azure Migrate 项目中跨所有工具跟踪迁移历程。


## <a name="movere"></a>Movere

Movere 是一个 SaaS 平台，它可以准确呈现整个 IT 环境在一天内的情况，以此增强商业智能。 随着组织的发展、变革和做出数字优化，该解决方案可让企业自信地洞察和控制其环境，而不管使用哪种平台和应用程序，或者在哪个地理位置运营。 Movere 已被 Microsoft [收购](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)，不再作为单独的产品/服务销售。  可以通过 Microsoft 解决方案评估和云经济计划获取 Movere。 [详细了解](https://www.movere.io) Movere。 

此外，我们建议考虑 Azure Migrate - 内置的迁移服务。 Azure Migrate 提供一个中心来简化迁移到云的过程。 该中心为不同的工作负荷（包括物理和虚拟服务器、数据库及应用程序）提供全面的支持。 借助端到端的可见性可以轻松跟踪整个发现、评估和迁移过程的进度。 此外，Azure Migrate 内置了 Azure 与合作伙伴的 ISV 工具，可提供丰富的功能，包括虚拟和物理服务器发现、基于性能的大小调整、成本规划、基于导入的评估，以及无代理应用程序依赖关系分析。 如果你希望通过专家来帮助自己入门，Microsoft 训练有素的 [Azure 专家托管服务提供商](https://azure.microsoft.com/partners)可引导你完成整个历程。 请查看 [Azure Migrate 网站](https://azure.microsoft.com/services/azure-migrate/)。 
 

## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务：

- **当前版本**：使用此版本创建 Azure Migrate 项目，发现本地计算机，并安排评估和迁移。 [详细了解](whats-new.md)此版本中的新增功能。
- **以前版本**：如果使用的是以前的 Azure Migrate 版本（仅支持评估本地 VMware VM），则现在应使用当前版本。 你无法再使用以前版本创建 Azure Migrate 项目，建议不要执行新的发现。 若要访问现有项目，请在 Azure 门户中搜索并选择“Azure Migrate”  。 “Azure Migrate”  仪表板上显示了一个通知和一个用于访问旧 Azure Migrate 项目的链接。



## <a name="next-steps"></a>后续步骤

- 尝试根据我们的教程评估 [VMware VM](tutorial-prepare-vmware.md)、[Hyper-V VM](tutorial-prepare-hyper-v.md) 或[物理服务器](tutorial-prepare-physical.md)。
- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。
