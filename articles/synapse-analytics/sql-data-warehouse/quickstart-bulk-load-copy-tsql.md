---
title: 快速入门：使用单个 T-SQL 语句批量加载数据
description: 使用 COPY 语句批量加载数据
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 06/18/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f82bedc6ef638714b2641003e8274c2024a86c2e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213000"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>快速入门：使用 COPY 语句批量加载数据

在本快速入门中，你将使用简单灵活的 [COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)将数据批量加载到 SQL 池中，以实现高吞吐量数据引入。 COPY 语句是推荐使用的加载实用工具，因为它提供以下功能，可以无缝且灵活地加载数据：

- 允许权限较低的用户进行加载，不需要对数据仓库有严格的控制权限
- 只使用一个 T-SQL 语句，不需要创建任何其他数据库对象
- 使用更精细的权限模型，无需使用共享访问签名 (SAS) 来公开存储帐户密钥
- 为 ERRORFILE 位置 (REJECTED_ROW_LOCATION) 指定一个不同的存储帐户
- 为每个目标列自定义默认值，并指定要加载到特定目标列中的源数据字段
- 为 CSV 文件指定自定义行终止符
- 为 CSV 文件转义字符串、字段和行分隔符
- 对 CSV 文件使用 SQL Server 日期格式
- 在存储位置路径中指定通配符和多个文件

## <a name="prerequisites"></a>先决条件

本快速入门假设你已有一个 SQL 池。 如果尚未创建 SQL 池，请使用[创建和连接 - 门户](create-data-warehouse-portal.md)快速入门。

## <a name="set-up-the-required-permissions"></a>设置所需权限

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>创建目标表

在此示例中，我们将从纽约出租车数据集加载数据。 我们将加载一个名为 Trip 的表，该表提供一年内的出租车行程。 运行下列语句来创建此表：

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>运行 COPY 语句

运行以下 COPY 语句，将数据从 Azure blob 存储帐户加载到 Trip 表中。

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>监视加载情况

通过定期运行以下查询来检查加载是否正在进行：

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>后续步骤

- 有关加载数据的最佳做法，请参阅[加载数据的最佳做法](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)。
- 若要了解如何管理数据加载的资源，请参阅[工作负载隔离](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)。 
