---
title: 将本地 SSIS 工作负荷迁移到 Azure 数据工厂中的 SSIS
description: 将本地 SSIS 工作负荷迁移到 ADF 中的 SSIS。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: a588a0977a4c6dcefaaefcfdcc542fee8b15466b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419062"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>将本地 SSIS 工作负载迁移到 ADF 中的 SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概述

将数据库工作负荷从本地 SQL Server 迁移到 Azure 数据库服务（即 Azure SQL 数据库或 Azure SQL 数据库托管实例）时，也需要迁移 SQL Server Integration Services (SSIS)（主要的增值服务之一）上的 ETL 工作负荷。

Azure 数据工厂 (ADF) 中 Azure-SSIS Integration Runtime (IR) 支持运行 SSIS 包。 预配 Azure-SSIS IR 后，可以使用熟悉的工具（例如 SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS)，以及 dtinstall/dtutil/dtexec 等命令行实用工具）在 Azure 中部署和运行包。 有关详细信息，请参阅 [Azure SSIS 直接迁移概述](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)。

本文重点介绍将 ETL 工作负荷从本地 SSIS 迁移到 ADF 中的 SSIS 的过程。 迁移过程包括以下两个阶段：**评估**和**迁移**。

## <a name="assessment"></a>评估

若要建立完整的迁移计划，可以进行全面的评估，以帮助识别源 SSIS 包中存在的会妨碍成功迁移的问题。

数据迁移助手 (DMA) 是出于此目的一个免费下载的工具，可在本地安装和执行。 可以创建“Integration Services”类型的 DMA 评估项目来分批评估 SSIS 包，并识别以下类别的兼容性问题：****

- 迁移阻止程序：这些是阻止在 Azure-SSIS IR 上运行的迁移源包的兼容性问题。 DMA 将提供指导来帮助你解决这些问题。

- 信息性问题：这些是源包中使用的部分支持或弃用功能。 DMA 将提供全面的建议、Azure 中可用的替代方法和缓解步骤来解决这些问题。

### <a name="four-storage-types-for-ssis-packages"></a>SSIS 包的四种存储类型

- SSIS 目录 (SSISDB)。 此存储是在 SQL Server 2012 中引入的，其中包含一组用于处理 SSIS 项目/包的存储过程、视图和表值函数。
- 文件系统。
- SQL Server 系统数据库 (MSDB)。
- SSIS 包存储。 这是位于两个子类型顶层的包管理层：
  - MSDB：SQL Server 中用于存储 SSIS 包的系统数据库。
  - 托管文件系统：SQL Server 安装路径中用于存储 SSIS 包的特定文件夹。

DMA 当前支持对自 **DMA 版本 5.0** 以来存储在**文件系统**、**包存储**和 **SSIS 目录**中的包进行批量评估。

获取 [DMA](https://docs.microsoft.com/sql/dma/dma-overview)，并[使用它来执行包评估](https://docs.microsoft.com/sql/dma/dma-assess-ssis)。

## <a name="migration"></a>迁移

根据源 SSIS 包的[存储类型](#four-storage-types-for-ssis-packages)和数据库工作负载的迁移目标，迁移**SSIS 包**和 SQL **Server 代理作业**的步骤可能会有所不同。 有两种情况：

- [**Azure SQL 数据库托管实例**作为数据库工作负载目标](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL 数据库**作为数据库工作负载目标](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>将 **Azure SQL 数据库托管实例**用作数据库工作负荷目标

| **包存储类型** |如何批量迁移 SSIS 包|如何批量迁移 SSIS 作业|
|-|-|-|
|SSISDB|[迁移 **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[将 SSIS 作业迁移到 Azure SQL 数据库托管实例代理](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|文件系统|通过 dtinstall/dtutil/手动复制将其重新部署到文件共享/Azure 文件，或将其保留在文件系统中，以通过 VNet/自承载 IR 进行访问。 有关详细信息，请参阅 [dtutil 实用工具](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|<li> 使用 SSMS 中的 [SSIS 作业迁移向导]进行迁移。（how-to-migrate-ssis-job-ssms.md） <li>通过脚本/SSMS/ADF 门户将其转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅 [SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL Server (MSDB)|通过 SSMS/dtutil 将其导出到文件系统/文件共享/Azure 文件。 有关详细信息，请参阅[导出 SSIS 包](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)。|通过脚本/SSMS/ADF 门户将其转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅 [SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|包存储区|通过 SSMS/dtutil 将其导出到文件系统/文件共享/Azure 文件，或通过 dtinstall/dtutil/手动复制将其重新部署到文件共享/Azure 文件，或将其保留在文件系统中，以通过 VNet/自承载 IR 进行访问。 有关详细信息，请参阅“dtutil 实用工具”。 有关详细信息，请参阅 [dtutil 实用工具](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通过脚本/SSMS/ADF 门户将其转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅 [SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

### <a name="azure-sql-database-as-database-workload-destination"></a>将 **Azure SQL 数据库**用作数据库工作负荷目标

| **包存储类型** |如何批量迁移 SSIS 包|如何批量迁移作业|
|-|-|-|
|SSISDB|通过 SSDT/SSMS 重新部署到 Azure-SSISDB。 有关详细信息，请参阅[在 Azure 中部署 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)。|通过脚本/SSMS/ADF 门户将其转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅 [SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|文件系统|通过 dtinstall/dtutil/手动复制将其重新部署到文件共享/Azure 文件，或将其保留在文件系统中，以通过 VNet/自承载 IR 进行访问。 有关详细信息，请参阅 [dtutil 实用工具](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|<li> 使用 SSMS 中的 [SSIS 作业迁移向导]进行迁移。（how-to-migrate-ssis-job-ssms.md） <li> 通过脚本/SSMS/ADF 门户将其转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅 [SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL Server (MSDB)|通过 SSMS/dtutil 将其导出到文件系统/文件共享/Azure 文件。 有关详细信息，请参阅[导出 SSIS 包](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)。|通过脚本/SSMS/ADF 门户将其转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅 [SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|包存储区|通过 SSMS/dtutil 将其导出到文件系统/文件共享/Azure 文件，或通过 dtinstall/dtutil/手动复制将其重新部署到文件共享/Azure 文件，或将其保留在文件系统中，以通过 VNet/自承载 IR 进行访问。 有关详细信息，请参阅“dtutil 实用工具”。 有关详细信息，请参阅 [dtutil 实用工具](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通过脚本/SSMS/ADF 门户将其转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅 [SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

## <a name="additional-resources"></a>其他资源

- [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)
- [数据迁移助手](https://docs.microsoft.com/sql/dma/dma-overview)
- [将 SSIS 工作负荷直接迁移到云](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [将 SSIS 包迁移到 Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [将包重新部署到 Azure SQL 数据库](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>后续步骤

- [验证部署到 Azure 的 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [运行部署在 Azure 中的 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [监视 Azure-SSIS 集成运行时](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [在 Azure 中计划 SSIS 包执行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
