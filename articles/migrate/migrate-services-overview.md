---
title: 关于 Azure Migrate
description: 了解 Azure Migrate 服务。
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 72f2dfb3e5e05466892d838af4e1c84c855b94a9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "86122075"
---
# <a name="about-azure-migrate"></a>关于 Azure Migrate

本文简要概述了 Azure Migrate 服务。

Azure Migrate 提供了一个集中化中心，用于评估本地服务器、基础结构、应用程序和数据并将其迁移到 Azure。 它提供以下内容：

- **统一迁移平台**：用于启动、运行和跟踪到 Azure 的迁移的单一门户。
- **工具范围**：用于评估和迁移的一系列工具。 Azure Migrate 工具包括服务器评估和 Azure Migrate:服务器迁移。 Azure Migrate 还集成了其他 Azure 服务和工具，以及独立软件供应商 (ISV) 产品/服务。
- **评估和迁移**：在 Azure Migrate 中心内，可以评估和迁移以下项目：
    - **服务器**：评估本地服务器并将其迁移到 Azure 虚拟机或 Azure VMware 解决方案 (AVS)（预览版）。
    - **数据库**：评估本地数据库，并将其迁移到 Azure SQL 数据库或 SQL 托管实例。
    - **Web 应用程序**：使用 Azure 应用服务迁移助手评估本地 Web 应用程序并将其迁移到 Azure 应用服务。
    - **虚拟桌面**：评估本地虚拟桌面基础结构 (VDI) 并将其迁移到 Azure 中的 Windows 虚拟桌面。
    - **Data**：使用 Azure Data Box 产品快速且经济高效地将大量数据迁移到 Azure。

## <a name="integrated-tools"></a>集成工具

Azure Migrate 中心包含以下工具：

**工具** | **评估和迁移** | **详细信息**
--- | --- | ---
**Azure Migrate:服务器评估** | 评估服务器。 | 在准备迁移到 Azure 时，发现并评估本地 VMware VM、Hyper-V VM 和物理服务器。
**Azure Migrate:服务器迁移** | 迁移服务器。 | 将 VMware VM、Hyper-V VM、物理服务器、其他虚拟机和公有云 VM 迁移到 Azure。
**Data Migration Assistant** | 评估 SQL Server 数据库，以便迁移到 Azure SQL 数据库、Azure SQL 托管实例或运行 SQL Server 的 Azure VM。 | 数据迁移助手可以帮助找出阻止迁移的潜在问题。 它可以识别不受支持的功能、迁移后可从中受益的新功能，以及正确的数据库迁移路径。 [了解详细信息](/sql/dma/dma-overview?view=sql-server-2017)。
**Azure 数据库迁移服务** | 将本地数据库迁移到运行 SQL Server 的 Azure VM、Azure SQL 数据库或 SQL 托管实例。 | [详细了解](../dms/dms-overview.md)数据库迁移服务。
**Movere** | 评估服务器。 | [详细了解](#movere) Movere。
**Web 应用迁移助手** | 评估本地 Web 应用并将其迁移到 Azure。 |  使用 Azure 应用服务迁移助手评估用于迁移到 Azure 应用服务的本地网站。<br/><br/> 使用迁移助手将 .NET 和 PHP Web 应用迁移到 Azure。 [详细了解](https://appmigration.microsoft.com/) Azure 应用服务迁移助手。
**Azure Data Box** | 迁移脱机数据。 | 使用 Azure Data Box 产品将大量脱机数据迁移到 Azure。 [了解详细信息](../databox/index.yml)。

> [!NOTE]
> 如果使用的是 Azure 政府版，则外部集成工具和 ISV 产品/服务无法将数据发送到 Azure Migrate 项目。 你可以单独使用工具。

## <a name="isv-integration"></a>ISV 集成

Azure Migrate 集成了多个 ISV 产品/服务。 

**ISV**    | **功能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | 迁移服务器。
[Cloudamize](https://www.cloudamize.com/platform) | 评估服务器。
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | 评估和迁移服务器。
[Device42](https://docs.device42.com/) | 评估服务器。
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 评估 VDI。
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 迁移服务器。
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | 评估服务器。
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | 评估服务器和数据库。

## <a name="azure-migrate-server-assessment-tool"></a>“Azure Migrate:服务器评估工具

Azure Migrate:服务器评估工具可发现并评估要迁移到 Azure 的本地 VMware VM、Hyper-V VM 和物理服务器。 

该工具的作用如下：

- **Azure 迁移就绪性**：评估本地计算机是否已准备好迁移到 Azure。
- **Azure 大小调整**：迁移后估算 Azure VM 大小或 Azure VMware 节点数。
- **Azure 成本估算**：估算在 Azure 中运行本地服务器的成本。
- **依赖关系分析**：确定跨服务器依赖关系和优化策略，以便将相互依赖的服务器移动到 Azure。 详细了解使用[依赖关系分析](concepts-dependency-visualization.md)进行的服务器评估。

服务器评估使用本地部署的轻型 [Azure Migrate 设备](migrate-appliance.md)。

- 该设备在 VM 或物理服务器上运行。 可以使用下载的模板轻松安装它。
- 设备会发现本地计算机。 它还持续向 Azure Migrate 发送计算机元数据和性能数据。
- 设备发现是无代理的。 无需在已发现的计算机上安装任何组件。
- 完成设备发现后，可将发现的计算机收集到组中，然后对每个组运行评估。

## <a name="azure-migrate-server-migration-tool"></a>“Azure Migrate:服务器迁移工具

Azure Migrate:服务器迁移工具可帮助你迁移到 Azure：

**迁移** | **详细信息**
--- | ---
本地 VMware VM | 可以使用无代理或基于代理的迁移将 VM 迁移到 Azure。<br/><br/> 对于无代理迁移，服务器迁移将使用你在本地部署的 Azure Migrate 设备。 它与用于服务器评估的设备类型相同。<br/><br/> 对于基于代理的迁移，服务器评估使用复制设备。
本地 Hyper-V VM | 将 VM 迁移到 Azure。<br/><br/> 服务器评估使用 Hyper-V 主机上安装的提供程序代理进行迁移。
本地物理服务器 | 可将物理计算机到迁移到 Azure。 还可迁移其他虚拟化计算机以及其他公有云中的 VM，方法是将其视为虚拟机以进行迁移。 | 服务器评估使用复制设备进行迁移。


## <a name="selecting-assessment-and-migration-tools"></a>选择评估和迁移工具

在 Azure Migrate 中心内，选择用于评估或迁移的工具，并将其添加到某个 Azure Migrate 项目。 如果添加 ISV 工具或 Movere：

- 若要开始使用该工具，请根据工具说明获取许可证或注册免费试用版。 每个 ISV 或工具都会指定工具许可。
- 每个工具都有一个用于连接到 Azure Migrate 的选项。 按照工具说明进行连接。
- 可以在 Azure Migrate 项目中跨所有工具跟踪迁移。

## <a name="movere"></a>Movere

Movere 是一个软件即服务 (SaaS) 平台。 它可以准确呈现整个 IT 环境在一天内的情况，从而增强商业智能。 组织和企业会发展、变革以及进行数字化优化。 在这种情况下，Movere 可让企业自信地洞察和控制其环境，不管使用哪个平台和应用程序，也不管在哪个地理位置运营。

Microsoft 已[收购](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)了 Movere，后者不再作为单独的产品/服务进行销售。 可以通过 Microsoft 解决方案评估和 Microsoft 云经济计划获取 Movere。 [详细了解](https://www.movere.io) Movere。

此外，我们建议考虑 Azure Migrate - 内置的迁移服务。 Azure Migrate 提供了一个集中化中心来简化云迁移。 该中心为工作负荷（例如物理和虚拟服务器、数据库及应用程序）提供全面的支持。 可以借助端到端的可见性轻松跟踪整个发现、评估和迁移过程的进度。

Azure Migrate 内置了 Azure 与合作伙伴的 ISV 工具，可提供丰富的功能，包括：

- 发现虚拟服务器和物理服务器。
- 基于性能的大小调整。
- 成本规划。
- 基于导入的评估。
- 对无代理应用程序进行依赖关系分析。

如果在入门的过程中需要专家帮助，请联系 Microsoft 训练有素的 [Azure 专家托管服务提供商](https://azure.microsoft.com/partners)来获取相关指导。 请查看 [Azure Migrate 网站](https://azure.microsoft.com/services/azure-migrate/)。 

## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务。

- **当前版本**：使用此版本创建 Azure Migrate 项目，发现本地计算机，并安排评估和迁移。 [详细了解](whats-new.md)此版本中的新增功能。
- **以前版本**：以前的 Azure Migrate 版本只支持评估本地 VMware VM。 如果使用的是旧版，则现在应使用新版。 无法再使用旧版创建 Azure Migrate 项目。 建议不要使用旧版执行新的发现操作。

    若要在 Azure 门户中访问现有项目，请搜索并选择“Azure Migrate”。 “Azure Migrate”仪表板有一个通知，还有一个用于访问旧 Azure Migrate 项目的链接。

## <a name="next-steps"></a>后续步骤

- 尝试根据我们的教程评估 [VMware VM](tutorial-prepare-vmware.md)、[Hyper-V VM](tutorial-prepare-hyper-v.md) 或[物理服务器](tutorial-prepare-physical.md)。
- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。
