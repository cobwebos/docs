---
title: Azure SQL Database 导入/导出服务需要很长时间才能导入或导出数据库 |Microsoft Docs
description: Azure SQL Database 导入/导出服务需要很长时间才能导入或导出数据库
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974456"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database 导入/导出服务需要很长时间才能导入或导出数据库

使用 Azure SQL 数据库导入/导出服务时，你可能会注意到，有时可能需要很长时间才能完成此过程。 本文提供了有关三个延迟的可能原因以及可用于解决这些问题的替代方法的其他信息。

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database 导入/导出服务

Azure SQL Database 导入/导出服务是一项基于 REST 的 web 服务，可在每个 Microsoft Azure 数据中心运行。 这是在使用[导入数据库](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal)或[导出](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal)选项来移动 Microsoft Azure 门户中的 SQL 数据库时调用的服务。 该服务提供一个免费的请求队列服务和一个免费计算服务，用于从 Microsoft Azure 的 SQL 数据库到 Microsoft Azure 二进制大型对象（BLOB）存储执行导入和导出。

导入和导出操作不是传统的物理数据库备份，而是使用特殊 BACPAC 格式的数据库的逻辑备份。 利用这种逻辑 BACPAC 格式，你可以避免使用不同版本的 SQL Server 和 SQL 数据库的物理格式。 因此，您可以使用它安全地将数据库还原到 SQL 数据库和 SQL Server 数据库。

## <a name="what-causes-the-process-to-take-a-long-time"></a>这会导致进程花费较长时间

Azure SQL Database 导入/导出服务提供了每个区域的有限数量的计算虚拟机（Vm）来处理导入和导出操作。 计算 VM 按区域进行托管，以确保导入或导出避免跨区域的带宽延迟和收费。 因此，如果在同一区域同时进行了太多请求，则在处理操作时，会出现大量的延迟。 完成请求所需的时间可能会相差几秒钟到几个小时。

> [!NOTE]
> 如果在四天内未处理请求，服务会自动取消请求。

## <a name="recommended-solutions"></a>建议的解决方案

如果你的数据库导出仅用于从意外的数据删除中进行恢复，则所有 Azure SQL Server 数据库版本均提供系统生成的备份的自助服务还原功能。 但如果出于其他原因需要这些导出，且需要的导入或导出性能持续更快或更多，则应考虑以下选项：

* [使用 SQLPackage 实用工具导出到 BACPAC 文件](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [使用 SQL Server Management Studio （SSMS）导出到 BACPAC 文件](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* 使用 Microsoft® SQL Server®数据层应用程序框架（DacFx） API，直接在代码中运行 BACPAC 导入或导出。 有关其他信息，请查看
  * [导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft SqlServer 命名空间](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [下载 DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>导出或导入 Azure SQL 数据库时的注意事项

* 本文中讨论的所有方法都使用 DTU 配额，导致 Azure SQLDB 服务受到限制。 可以在[Azure 门户上查看数据库的 DTU 统计](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance)信息。 如果数据库命中了资源限制，则[升级服务层](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)以添加更多的资源。
* 客户端应用程序（如 sqlpackage 实用工具或自定义 DAC 应用程序）应理想地从 SQL 数据库所在的同一区域中的虚拟机（VM）运行，否则你可能会遇到网络延迟导致的性能问题。
* 导出没有聚集索引的大型表可能会很慢，甚至会导致失败。 这是因为不能并行拆分和导出表，并且必须将其导出到一个事务中，这会导致在导出过程中出现缓慢和潜在故障，尤其是对于大型表。 


## <a name="related-documents"></a>相关文档

[导出 Azure SQL 数据库时的注意事项](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
