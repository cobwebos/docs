---
title: 导入/导出服务花费较长时间
description: Azure SQL Database 导入/导出服务需要很长时间才能导入或导出数据库
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 29edd7da42a5eb4586cd52cc0f913a56e199a5ba
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808281"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database 导入/导出服务需要很长时间才能导入或导出数据库

使用 Azure SQL 数据库导入/导出服务时，该进程可能需要比预期更长的时间。 本文介绍了此延迟的可能原因和替代方法。

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database 导入/导出服务

Azure SQL Database 导入/导出服务是一项基于 REST 的 web 服务，可在每个 Azure 数据中心运行。 使用 "[导入数据库](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal)" 或 "[导出](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal)" 选项在 Azure 门户中移动 SQL 数据库时，将调用此服务。 该服务提供免费的请求队列和计算服务，用于在 Azure SQL 数据库和 Azure Blob 存储之间执行导入和导出。

导入和导出操作不表示传统的物理数据库备份，而是使用特殊 BACPAC 格式的数据库的逻辑备份。 BACPAC 格式使你可以避免使用可能因不同版本的 Microsoft SQL Server 和 Azure SQL 数据库而异的物理格式。 因此，您可以使用它安全地将数据库还原到 SQL Server 数据库和 SQL 数据库。

## <a name="what-causes-delays-in-the-process"></a>在此过程中导致延迟的原因是什么？

Azure SQL Database 导入/导出服务提供了每个区域的有限数量的计算虚拟机（Vm）来处理导入和导出操作。 计算 Vm 按区域进行托管，以确保导入或导出避免跨区域的带宽延迟和收费。 如果在同一区域同时进行了太多请求，则处理这些操作时可能会出现大量的延迟。 完成请求所需的时间可能会相差几秒钟到几个小时。

> [!NOTE]
> 如果在四天内未处理请求，服务会自动取消请求。

## <a name="recommended-solutions"></a>建议的解决方案

如果你的数据库导出仅用于从意外的数据删除中进行恢复，则所有 Azure SQL 数据库版本均提供系统生成的备份的自助服务还原功能。 但如果出于其他原因需要这些导出，且需要的导入/导出性能持续更快或更多，请考虑以下选项：

* [使用 SQLPackage 实用工具导出到 BACPAC 文件](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)。
* [使用 SQL Server Management Studio （SSMS）导出到 BACPAC 文件](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)。
* 使用 Microsoft SQL Server Data-Tier Application Framework （DacFx） API 在代码中直接运行 BACPAC 导入或导出。 有关其他信息，请参阅：
  * [导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft SqlServer 命名空间](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [下载 DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>导出或导入 Azure SQL 数据库时要考虑的事项

* 本文中讨论的所有方法都使用数据库事务单位（DTU）配额，这会导致 Azure SQL 数据库服务的限制。 可以在[Azure 门户上查看数据库的 DTU 统计](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance)信息。 如果数据库已达到其资源限制，请[升级服务层](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)以添加更多资源。
* 理想情况下，应从与 SQL 数据库相同的区域中的 VM 运行客户端应用程序（如 sqlpackage 实用工具或自定义 DAC 应用程序）。 否则，可能会遇到与网络延迟相关的性能问题。
* 导出没有聚集索引的大型表可能会非常慢，甚至会导致失败。 出现此行为是因为不能同时拆分和导出表。 相反，它必须在单个事务中导出，这会导致性能下降和导出期间的可能失败，尤其是对于大型表。


## <a name="related-documents"></a>相关文档

[导出 Azure SQL 数据库时的注意事项](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
