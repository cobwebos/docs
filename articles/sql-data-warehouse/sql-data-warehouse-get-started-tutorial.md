---
title: "Azure SQL 数据仓库 - 入门教程 | Microsoft 文档"
description: "Azure SQL 数据仓库的入门教程。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: fe9de0ffad3fe5d4acbf3caf2f08101f6a13daaf
ms.openlocfilehash: 12f500c01671799612b0d1988e0d07bbfda600da


---
# <a name="get-started-with-sql-data-warehouse"></a>SQL 数据仓库入门

Azure SQL 数据仓库的入门教程。 本教程讲解有关预配 SQL 数据仓库、在其中载入数据的基础知识，以及有关缩放、暂停和优化的一些基础知识。 

**估计完成时间：**75 分钟

## <a name="prerequisites"></a>先决条件


### <a name="sign-up-for-microsoft-azure"></a>注册 Microsoft Azure
如果你没有 Microsoft Azure 帐户，必须注册一个帐户才能使用此服务。 如果已有帐户，则可以跳过此步骤。 

1. 导航到帐户页 [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. 创建免费 Azure 帐户，或购买一个帐户。
3. 遵照说明操作

### <a name="install-appropriate-sql-client-driver-and-tools"></a>安装相应的 SQL 客户端驱动程序和工具

大多数 SQL 客户端工具可以使用 JDBC、ODBC 或 ADO.net 连接到 Azure SQL 数据仓库。 由于产品的复杂性并且 SQL 数据仓库支持大量的 T-SQL 功能，并非每个客户端应用程序都与 SQL 数据仓库完全兼容。

如果运行的是 Windows 操作系统，我们建议使用 [Visual Studio] 或 [SQL Server Management Studio]。


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>创建 Azure SQL 数据仓库

> [!NOTE]
> 创建 SQL 数据仓库可能会导致新的计费服务。  有关详细信息，请参阅 [Azure SQL 数据仓库定价](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。
>


### <a name="create-a-sql-data-warehouse"></a>创建 SQL 数据仓库
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“新建” > “数据库” > “SQL 数据仓库”。

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. 填写部署详细信息

    **数据库名称**：填写所需的任何名称。 如果有多个 SQL 数据仓库实例，我们建议在名称中包含区域、环境等详细信息，例如 *mydw-westus-1-test*

    **订阅**：你的 Azure 订阅

    **资源组**：新建一个资源组（如果打算对其他服务使用你的 Azure SQL 数据仓库，则可以使用现有的资源组）
    > [!NOTE]
    > 资源组中的服务应具有相同的生命周期。 资源组可用于资源管理，例如，指定访问控制和模板化部署的范围。 在[此处](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)阅读有关 Azure 资源组的详细信息。
    >

    **源**：空白数据库

    **服务器**：选择在[先决条件]部分中创建的服务器。

    **排序规则**：保留默认的排序规则 SQL_Latin1_General_CP1_CI_AS。

    **选择性能**：建议保持使用标准 400DWU

4. 选择“固定到仪表板”
   “固定到仪表板”**** ![](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. 请休息一下，等待完成 Azure SQL 数据仓库的部署！ 在正常情况下，完成此过程需要几分钟时间。 部署完实例后，门户会发出通知。 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>通过 SQL Server（逻辑服务器）连接到 Azure SQL 数据仓库

本教程使用 SQL Server Management Studio 连接到 SQL 数据仓库。 也可以其他工具通过以下受支持的连接器连接到 SQL 数据仓库：ADO.NET、JDBC、ODBC 和 PHP。 请记住，如果使用 Microsoft 不支持的工具，功能可能会受到限制。


### <a name="get-connection-information"></a>获取连接信息

若要连接到 SQL 数据仓库，必须通过在[先决条件]部分中创建的 SQL Server（逻辑服务器）进行连接。

1. 从仪表板中选择 SQL 数据仓库，或者在资源中搜索。

    ![SQL 数据仓库仪表板](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. 查找逻辑服务器的完整名称。

    ![选择服务器名称](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. 打开 SSMS，使用对象资源管理器通过在[先决条件]部分中创建的凭据连接到此服务器

    ![使用 SSMS 进行连接](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

如果一切正常，应会立即连接到 SQL Server（逻辑服务器）实例。 可以使用服务器凭据，以数据库所有者的身份通过服务器上任何数据库的身份验证。 但是，最佳做法是针对每个数据库创建单独的登录名和用户。 请参阅[为 SQL 数据仓库创建用户](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse)，了解如何创建用户。 

## <a name="create-a-user-for-sql-data-warehouse"></a>为 SQL 数据仓库创建用户

### <a name="why-create-a-separate-user"></a>为何要创建单独的用户？

我们将使用在前一步骤中创建的服务器凭据，通过与 SQL Server（逻辑服务器）建立的连接来为 SQL 数据仓库创建新用户。 为 SQL 数据仓库创建单独的用户/登录名的主要原因有两个。

1.  组织的用户应使用不同的帐户进行身份验证。 这样，便可以限制授予应用程序的权限，降低恶意活动的风险。

2. 默认情况下，当前与你关联的服务器管理员登录名使用较小的资源类。 资源类有助于控制针对查询的内存分配和 CPU 周期。 **smallrc** 中的用户获得的内存量较小，但可以利用更高的并发性。 与之相反，分配到 **xlargerc** 的用户获得的内存量较大，因此可以并发运行的查询数较少。 若要在加载数据的同时最大程度地优化压缩，需确保加载数据的用户属于较大的资源类。 在[此处](./sql-data-warehouse-develop-concurrency.md#resource-classes)阅读有关资源类的详细信息：

### <a name="creating-a-user-of-a-larger-resource-class"></a>创建较大资源类的用户

1. 查询服务器的 **master** 数据库

    ![针对 Master 新建查询](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![针对 Master1 新建查询](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. 创建服务器登录名和用户

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. 查询 SQL 数据仓库数据库，基于服务器登录名创建新的数据库用户 
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. 向用户授予数据库控制权限
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > 如果数据库名称包含连字符，请务必将名称括在方括号中！ 
    >

5. 将数据库用户添加到 **xlargerc** 资源类角色
    ```sql
    EXEC sp_addrolemember 'xlargerc', 'LoadingUser';
    ```

6. 使用新凭据登录到数据库

    ![使用新登录名登录](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>加载数据

### <a name="defining-external-data"></a>定义外部数据
1. 创建主密钥并定义外部数据源

    ```sql
    CREATE MASTER KEY;

    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. 定义外部文件格式

    ```CREATE EXTERNAL FILE FORMAT``` 命令用于指定要从中加载数据的外部数据格式。 对于纽约公共出租车数据，我们已使用两种格式在 Azure Blob 存储中存储数据

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  为外部文件格式创建架构

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. 创建外部表。 这些表引用 HDFS 或 Azure Blob 存储中存储的数据。 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
    (
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
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
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>Create Table as Select (CTAS)

5. 将外部表中的数据载入 SQL 数据仓库实例。 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > 假设我们要加载几个 GB 的数据，然后将其压缩成高性能群集列存储索引。 此时，可以运行以下 DMV 查询，然后休息片刻，让 Azure SQL 数据仓库执行一些繁重的传输任务。
    >

6. 创建新查询，然后使用此动态管理视图 (DMV) 观察数据的传入过程

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. 查看所有系统查询

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. 结果让人欣慰，数据已顺利载入 Azure SQL 数据仓库

    ![查看加载的数据](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>查询数据 

### <a name="scan-query-with-scaling"></a>使用缩放扫描查询

我们不妨了解一下，缩放对查询的速度会造成怎样的影响。

在开始之前，让我们将操作缩减到 100 个 DWU，想像单独一个计算节点的表现如何。

1. 转到门户，选择你的 SQL 数据仓库实例

2. 在“SQL 数据仓库”边栏选项卡中选择“缩放”。 

    ![在门户中缩放数据仓库](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. 将性能条缩减到 100 个 DWU，然后单击“保存”。

    ![缩放并保存](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. 等到缩放操作完成。

    > [!NOTE]
    > 请注意，缩放操作会**终止**当前正在运行的查询，并阻止运行新的查询。
    >
    
5. 选择所有列中的前&100; 万个条目，针对行程数据执行扫描操作。 如果希望很快就能获得结果，请选择更少的行。

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

记下运行此操作所花费的时间。

6. 将实例扩展到 400 个 DWU。 请记住，每增加 100 个 DWU，就会向 Azure SQL 数据仓库添加一个计算节点。

7. 再次运行查询！ 应会看到明显的差异。 

> [!NOTE]
> Azure SQL 数据仓库是一个大规模并行处理 (MPP) 平台。 可在不同节点之间并行工作的查询和操作会体验到 Azure SQL 数据仓库的真正强大威力。
>

### <a name="join-query-with-statistics"></a>针对统计信息运行联接查询

1. 运行可将“日期”表与“行程”表相联接的查询

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    与你可能预料到的一样，数据分布在各个节点时，查询花费的时间要长得多，尤其是在类似此查询的联接方案中。

2. 让我们运行以下语句，看看基于所联接的列创建统计信息时此查询会有什么不同：

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL 数据仓库不会自动管理统计信息。 统计信息对于查询性能非常重要，我们强烈建议创建并更新统计信息。
    > 
    > **基于涉及联接的列、WHERE 子句中使用的列以及 GROUP BY 中的列创建信息统计可以获得最大的效益。**
    >

3. 再次根据“先决条件”中的步骤运行查询，然后观察性能是否出现任何差异。 尽管查询性能的差异不像扩展实例那样明显，但我们仍会注意到速度有一定程度的上升。 

## <a name="next-steps"></a>后续步骤

现在，可以查询和浏览数据。 请查看我们的最佳实践或提示。

在完成一天的浏览工作后，请务必暂停你的实例！ 在生产环境中，根据业务需要暂停和缩放实例可以大幅节省成本。

![暂停](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>有用的资料

[并发性和工作负荷管理]

[Azure SQL 数据仓库最佳实践]

[查询监视]

[有关构建大规模关系数据仓库的前 10 条最佳实践]

[将数据迁移到 Azure SQL 数据仓库]


[并发性和工作负荷管理]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Azure SQL 数据仓库最佳实践]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[查询监视]: sql-data-warehouse-manage-monitor.md
[有关构建大规模关系数据仓库的前 10 条最佳实践]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[将数据迁移到 Azure SQL 数据仓库]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[先决条件]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO3-->


