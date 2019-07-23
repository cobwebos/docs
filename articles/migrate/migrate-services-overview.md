---
title: 关于 Azure Migrate | Microsoft Docs
description: 概述 Azure Migrate 服务。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5409ed799454a6bb64077ee884065fc518556142
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227807"
---
# <a name="about-azure-migrate"></a>关于 Azure Migrate

本文简要概述了 Azure Migrate。

Azure Migrate 可帮助你迁移到 Azure。 Azure Migrate 提供了一个中心，用于跟踪 Azure 的本地基础结构、应用程序和数据的发现、评估以及迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。 它提供：

- **统一迁移平台**：使用单一门户启动、运行和跟踪到 Azure 的迁移过程。
- **工具范围**：Azure Migrate 提供本机工具，并与其他 Azure 服务以及 ISV 工具集成。 根据组织的要求选择适当的评估和迁移工具。 
- **Azure Migrate 服务器评估**：使用服务器评估工具评估用于迁移到 Azure 的本地 VMware VM 和 Hyper-V VM。
- **Azure Migrate 服务器迁移**：使用服务器迁移工具将本地 VMware VM、Hyper-V VM、云 VM 和物理服务器迁移到 Azure。
- **Azure Migrate 数据库评估**：评估要迁移到 Azure 的本地资源。
- **Azure Migrate 数据库迁移**：将本地数据库迁移到 Azure。


## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务：

- **当前版本**：使用此版本创建 Azure Migrate 项目，发现本地计算机，并安排评估和迁移。 [详细了解](whats-new.md)此版本中的新增功能。
- **以前版本**：如果你使用的是以前版本的 Azure Migrate（仅支持对本地 VMware VM 的评估），则现在应使用当前版本。 你无法再使用以前版本创建 Azure Migrate 项目，建议不要执行新的发现。 若要访问现有项目，请在 Azure 门户中选择“所有服务”，然后搜索 Azure Migrate   。 Azure Migrate 仪表板上提供了一个通知和一个用于访问旧 Azure Migrate 项目的链接。

## <a name="isv-integration"></a>ISV 集成

除本机 Azure 工具外，Azure Migrate 还与大量 ISV 产品/服务集成。 确定所需的工具，并将其添加到 Azure Migrate 项目。 可以跨 Azure 和 ISV 工具集中跟踪 Azure Migrate 项目内的迁移过程。

**ISV** | **功能**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | 评估
[设备 42](https://docs.device42.com/) | 评估
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | 评估
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | 评估
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | 评估和迁移
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | 迁移

### <a name="selecting-an-isv-tool"></a>选择 ISV 工具

将 ISV 工具添加到 Azure Migrate 项目后，通过获取许可证或注册免费试用版（根据 ISV 策略）即可开始使用该工具。 每个工具中都有一个用于连接到 Azure Migrate 的选项。 按照工具说明和文档操作，将该工具与 Azure Migrate 连接起来。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate 服务器评估

Azure Migrate 服务器评估可发现并评估本地 VMware VM 和 Hyper-V VM，以便迁移到 Azure。 它有助于识别以下内容:

- **Azure 迁移就绪性：** 评估本地计算机是否已准备好迁移到 Azure。
- **Azure 大小调整：** 迁移后估计 Azure VM 的大小。
- **Azure 成本估算：** 估算在 Azure 中运行本地服务器的成本。
- **依赖关系可视化：** 确定跨服务器依赖关系，以及将从属服务器移动到 Azure 的最佳方式。 

服务器评估使用在本地部署并向服务器评估注册的轻型设备。

- 该设备发现本地计算机，连接到服务器评估，并持续向 Azure Migrate 发送与性能相关的元数据。
- 发现是无代理的。 无需在已发现的 VM 上安装任何内容。
- 发现计算机后，将其集中到通常由要一起迁移的 VM 组成的组中。
- 为组创建评估。 随后便可分析评估，以确定迁移策略。

## <a name="azure-migrate-server-migration"></a>Azure Migrate 服务器迁移

Azure Migrate 服务迁移有助于将本地 VMware VM、Hyper-V VM、物理服务器、其他虚拟机和公有云 VM 迁移到 Azure。 可在评估计算机后进行迁移，也可不评估直接进行迁移。 

## <a name="azure-migrate-database-assessment"></a>Azure Migrate 数据库评估

Azure Migrate 与数据迁移助手 (DMA) 集成，用于评估本地 SQL Server 数据库以迁移到 Azure SQL DB、Azure SQL 托管实例或运行 SQL Server 的 Azure VM。 DMA 提供有关迁移的潜在障碍问题信息。 它确定不受支持的功能，以及迁移后可从中受益的新功能，并帮助确定数据库迁移的正确路径。 [了解详细信息](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。


## <a name="azure-migrate-database-migration"></a>Azure Migrate 数据库迁移

Azure Migrate 与 Azure 数据库迁移服务 (DMS) 集成，以将本地数据库迁移到 Azure。 使用 DMS 将本地数据库迁移到运行 SQL 的 Azure VM、Azure SQL DB 和 Azure SQL 托管实例。 [了解详细信息](https://docs.microsoft.com/azure/dms/dms-overview)。

## <a name="web-app-assessment-and-migration"></a>Web 应用评估和迁移

从 Azure Migrate 中心，可以评估本地 Web 应用并将其迁移到 Azure。

- **联机评估 Web 应用**：使用 Azure 应用服务迁移助手评估用于迁移到 Azure 应用服务的本地网站。
- **迁移 Web 应用**：使用 Azure 应用服务迁移助手将 .NET 和 PHP Web 应用迁移到 Azure。

[了解详细信息。](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>脱机数据迁移

可使用 Data Box 脱机产品系列将大量数据移到 Azure。 [了解详细信息](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>后续步骤

- 试用我们的教程来评估 [VMware VM](tutorial-assess-vmware.md) 和 [Hyper-V VM](tutorial-assess-hyper-v.md)。
- [详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。
- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。
