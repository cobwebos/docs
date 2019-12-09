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
ms.openlocfilehash: 52629b8e2e190cc041116e6f65488480712baf01
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929794"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>将本地 SSIS 工作负荷迁移到 ADF 中的 SSIS

## <a name="overview"></a>概述

将数据库工作负荷从本地 SQL Server 迁移到 Azure 数据库服务（即 Azure SQL 数据库或 Azure SQL 数据库托管实例）时，你的 ETL 工作负荷将 SQL Server Integration Services （SSIS）添加还需要迁移服务。

Azure 数据工厂（ADF）中 Azure-SSIS Integration Runtime （IR）支持运行 SSIS 包。 设置 Azure-SSIS IR 后，可以使用熟悉的工具（如 SQL Server Data Tools （SSDT）/SQL Server Management Studio （SSMS）和命令行实用工具（如 dtinstall/dtutil/dtexec）在 Azure 中部署和运行包。 有关详细信息，请参阅[AZURE SSIS 提升和转换概述](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)。

本文重点介绍如何将 ETL 工作负荷从本地 SSIS 迁移到 ADF 中的 SSIS。 迁移过程包括两个阶段：**评估**和**迁移**。

## <a name="assessment"></a>评估

若要建立完整的迁移计划，一种全面的评估可帮助确定源 SSIS 包的问题，这些问题会妨碍成功迁移。

数据迁移助手（DMA）是一个可免费下载的工具，可用于在本地安装和执行此目的。 可以创建类型**Integration Services**的 DMA 评估项目，以便成批评估 SSIS 包，并确定以下类别中显示的兼容性问题：

- 迁移阻止程序：这些是阻止迁移源包在 Azure-SSIS IR 上运行的兼容性问题。 DMA 提供帮助你解决这些问题的指南。

- 信息性问题：这些是在源包中使用的部分支持或弃用的功能。 DMA 提供一套全面的建议、Azure 中可用的替代方法和缓解步骤。

### <a name="four-storage-types-for-ssis-packages"></a>四种 SSIS 包存储类型

- SSIS 目录（SSISDB）。 这是 SQL Server 2012 引入的，其中包含一组用于处理 SSIS 项目/包的存储过程、视图和表值函数。
- 文件系统。
- SQL Server 系统数据库（MSDB）。
- SSIS 包存储区。 这是位于两个子类型之上的包管理层：
  - MSDB，它是 SQL Server 用于存储 SSIS 包的中的系统数据库。
  - 托管文件系统，它是 SQL Server 安装路径中的特定文件夹，用于存储 SSIS 包。

自**dma 版本5.0 版**起，dma 目前支持对**文件系统**、**包存储区**和**SSIS 目录**中存储的包进行批评估。

获取[DMA](https://docs.microsoft.com/sql/dma/dma-overview)，并对[其执行包评估](https://docs.microsoft.com/sql/dma/dma-assess-ssis)。

## <a name="migration"></a>迁移

根据源 SSIS 包的[存储类型](#four-storage-types-for-ssis-packages)和数据库工作负荷的迁移目标 **，迁移 ssis 包执行**和**SQL Server 代理作业**的步骤可能会有所不同。 有两个方案：

- [作为数据库工作负荷目标的**AZURE SQL 数据库托管实例**](#azure-sql-database-managed-instance-as-database-workload-destination)
- [作为数据库工作负荷目标的**AZURE SQL 数据库**](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>作为数据库工作负荷目标的**AZURE SQL 数据库托管实例**

| **包存储类型** |如何对 SSIS 包进行批处理迁移|如何批处理迁移 SSIS 作业|
|-|-|-|
|SSISDB|[迁移**SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[将 SSIS 作业迁移到 Azure SQL 数据库托管实例代理](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|文件系统|通过 dtinstall/dtutil/手动复制将它们重新部署到文件共享/Azure 文件，或通过 VNet/自承载 IR 保留在文件系统中。 有关详细信息，请参阅[dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通过脚本/SSMS/ADF 门户将它们转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅[SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL Server （MSDB）|通过 SSMS/dtutil 将它们导出到文件系统/文件共享/Azure 文件。 有关详细信息，请参阅[导出 SSIS 包](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)。|通过脚本/SSMS/ADF 门户将它们转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅[SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|包存储区|通过 SSMS/dtutil 将它们导出到文件系统/文件共享/Azure 文件，或通过 dtinstall/dtutil/手动复制将它们重新部署到文件共享/Azure 文件中，或将它们保留在文件系统中以通过 VNet/自承载 IR 进行访问。 有关详细信息，请参阅 dtutil utility。 有关详细信息，请参阅[dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通过脚本/SSMS/ADF 门户将它们转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅[SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

### <a name="azure-sql-database-as-database-workload-destination"></a>作为数据库工作负荷目标的**AZURE SQL 数据库**

| **包存储类型** |如何对 SSIS 包进行批处理迁移|如何批处理迁移作业|
|-|-|-|
|SSISDB|通过 SSDT/SSMS 重新部署到 Azure。 有关详细信息，请参阅[在 Azure 中部署 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)。|通过脚本/SSMS/ADF 门户将它们转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅[SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|文件系统|通过 dtinstall/dtutil/手动复制将它们重新部署到文件共享/Azure 文件，或通过 VNet/自承载 IR 保留在文件系统中。 有关详细信息，请参阅[dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通过脚本/SSMS/ADF 门户将它们转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅[SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL Server （MSDB）|通过 SSMS/dtutil 将它们导出到文件系统/文件共享/Azure 文件。 有关详细信息，请参阅[导出 SSIS 包](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)。|通过脚本/SSMS/ADF 门户将它们转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅[SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|包存储区|通过 SSMS/dtutil 将它们导出到文件系统/文件共享/Azure 文件，或通过 dtinstall/dtutil/手动复制将它们重新部署到文件共享/Azure 文件中，或将它们保留在文件系统中以通过 VNet/自承载 IR 进行访问。 有关详细信息，请参阅 dtutil utility。 有关详细信息，请参阅[dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通过脚本/SSMS/ADF 门户将它们转换为 ADF 管道/活动/触发器。 有关详细信息，请参阅[SSMS 计划功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

## <a name="additional-resources"></a>其他资源

- [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)
- [数据迁移助手](https://docs.microsoft.com/sql/dma/dma-overview)
- [将 SSIS 工作负荷直接迁移到云](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [将 SSIS 包迁移到 Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [将包重新部署到 Azure SQL 数据库](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>后续步骤

- [验证部署到 Azure 的 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [运行在 Azure 中部署的 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [监视 Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [计划 Azure 中的 SSIS 包执行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
