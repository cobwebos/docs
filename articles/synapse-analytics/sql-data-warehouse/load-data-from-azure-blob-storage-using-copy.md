---
title: 教程：加载纽约出租车数据
description: 教程使用 Azure 门户和 SQL Server Management Studio 从 Synapse SQL 的 Azure Blob 加载纽约出租车数据。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/31/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d2c2673e6863725e064f3ad8561ab77eb1b051eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371518"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>教程：加载纽约出租车数据集

本教程使用 [COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)从 Azure Blob 存储帐户加载纽约出租车数据。 本教程使用 [Azure 门户](https://portal.azure.com)和 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) 执行以下操作：

> [!div class="checklist"]
>
> * 在 Azure 门户中创建 SQL 池
> * 在 Azure 门户中设置服务器级防火墙规则
> * 使用 SSMS 连接到数据仓库
> * 创建专用于加载数据的用户
> * 为示例数据集创建表 
> * 使用 COPY T-SQL 语句将数据加载到数据仓库
> * 查看正在加载的数据的进度

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>准备阶段

开始本教程之前，请下载并安装最新版 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS)。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-blank-database"></a>创建空数据库

SQL 池是使用定义的一组[计算资源](memory-concurrency-limits.md)创建的。 数据库在 [Azure 资源组](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)和[逻辑 SQL 服务器](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中创建。

按照以下步骤创建空白的数据库。

1. 在 Azure 门户的左上角选择“创建资源”。

2. 在“新建”页中选择“数据库”，然后在“新建”页上的“特色”下选择“Azure Synapse Analytics”    。

    ![创建数据仓库](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. 使用以下信息填写窗体：

   | 设置            | 建议的值       | 说明                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *名称**            | mySampleDataWarehouse | 如需有效的数据库名称，请参阅 [Database Identifiers](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)（数据库标识符）。 |
   | **订阅**   | 订阅     | 有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   | **资源组** | myResourceGroup       | 如需有效的资源组名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)（命名规则和限制）。 |
   | **选择源**  | 空白数据库        | 指定创建空白数据库。 请注意，数据仓库是一种数据库。 |

    ![创建数据仓库](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. 选择“服务器”，为新数据库创建并配置新服务器。 使用以下信息填写“新建服务器”窗体：

    | 设置                | 建议的值          | 说明                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **服务器名称**        | 任何全局唯一名称 | 如需有效的服务器名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)（命名规则和限制）。 |
    | 服务器管理员登录名 | 任何有效的名称           | 如需有效的登录名，请参阅 [Database Identifiers](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)（数据库标识符）。 |
    | **密码**           | 任何有效的密码       | 密码必须至少有八个字符，且必须包含以下类别中的三个类别的字符：大写字符、小写字符、数字以及非字母数字字符。 |
    | **位置**           | 任何有效的位置       | 有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。 |

    ![创建服务器](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. 选择“选择”。

6. 选择“性能级别”，指定数据仓库是 Gen1 还是 Gen2，以及数据仓库单位的数量。

7. 对于本教程，请选择 SQL 池“Gen2”。 滑块默认设置为“DW1000c”。  请尝试上下移动滑块，以查看其工作原理。

    ![配置性能](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. 选择“应用”。
9. 在预配边栏选项卡中，为空白数据库选择一个排序规则。 对于本教程，请使用默认值。 有关排序规则的详细信息，请参阅 [Collations](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)（排序规则）

10. 填写窗体后，请选择“创建”以预配数据库。 预配需要数分钟。

11. 在工具栏上，选择“通知”监视部署过程。
  
     ![屏幕截图显示在部署正在进行时打开的通知窗格 Azure 门户。](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>创建服务器级防火墙规则

服务器级别的防火墙会阻止外部应用程序和工具连接到服务器或服务器上的任何数据库。 要启用连接，可以添加防火墙规则，为特定 IP 地址启用连接。  按照以下步骤为客户端的 IP 地址创建[服务器级防火墙规则](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

> [!NOTE]
> Azure Synapse Analytics 通过端口1433进行通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到服务器，除非 IT 部门打开了端口 1433。

1. 部署完成后，在左侧菜单中选择“SQL 数据库”，然后在“SQL 数据库”页上选择“mySampleDatabase”。 此时会打开数据库的概览页，其中显示了完全限定的服务器名称（例如 mynewserver-20180430.database.windows.net），并提供了其他配置的选项****。

2. 在后续的快速入门中，请复制此完全限定的服务器名称，将其用于连接到服务器及其数据库。 然后选择服务器名称，打开服务器设置。

    ![查找服务器名称](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. 选择服务器名称，打开服务器设置。

    ![服务器设置](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. 选择“显示防火墙设置”。 此时会打开服务器的“防火墙设置”页面。

    ![服务器防火墙规则](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. 在工具栏上选择“添加客户端 IP”，将当前的 IP 地址添加到新的防火墙规则。 防火墙规则可以针对单个 IP 地址或一系列 IP 地址打开端口 1433。

6. 选择“保存” 。 此时会针对当前的 IP 地址创建服务器级防火墙规则，在服务器上打开端口 1433。

7. 选择“确定”，然后关闭“防火墙设置”页。 

现在，可使用此 IP 地址连接到服务器及其数据仓库。 可从 SQL Server Management Studio 或另一种所选工具进行连接。 连接时，请使用之前创建的 ServerAdmin 帐户。  

> [!IMPORTANT]
> 默认情况下，所有 Azure 服务都允许通过 SQL 数据库防火墙进行访问。 在此页上选择“关闭”，然后选择“保存”，对所有 Azure 服务禁用防火墙。 

## <a name="get-the-fully-qualified-server-name"></a>获取完全限定的服务器名称

请在 Azure 门户中获取服务器的完全限定的服务器名称。 稍后，在连接到服务器时，将使用该完全限定的名称。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左侧菜单中选择“Azure Synapse Analytics”，然后在“Azure Synapse Analytics”页上选择你的数据库。 
3. 在数据库的“Azure 门户”页的“概要”窗格中，找到并复制“服务器名称”。  在此示例中，完全限定名称为 mynewserver-20180430.database.windows.net。

    ![连接信息](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>以服务器管理员的身份连接到服务器

本部分使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) 来建立与服务器的连接。

1. 打开 SQL Server Management Studio。

2. 在“连接到服务器”对话框中，输入以下信息：

    | 设置        | 建议的值                            | 说明                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | 服务器类型    | 数据库引擎                            | 此值是必需的                                       |
    | 服务器名称    | 完全限定的服务器名称            | 该名称应类似于 mynewserver-20180430.database.windows.net****。 |
    | 身份验证 | SQL Server 身份验证                  | SQL 身份验证是本教程中配置的唯一身份验证类型。 |
    | 登录          | 服务器管理员帐户                   | 这是在创建服务器时指定的帐户。 |
    | 密码       | 服务器管理员帐户的密码 | 这是在创建服务器时指定的密码。 |

    ![连接到服务器](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. 选择“连接” 。 此时会在 SSMS 中打开“对象资源管理器”窗口。

4. 在“对象资源管理器”中，展开“数据库”。 然后展开“系统数据库”和“master”，查看 master 数据库中的对象。  展开“mySampleDatabase”，查看新数据库中的对象。

    ![数据库对象](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>创建用于加载数据的用户

服务器管理员帐户用于执行管理操作，不适合对用户数据运行查询。 加载数据是一种内存密集型操作。 内存最大值根据配置的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)和[资源类](resource-classes-for-workload-management.md)定义。

最好创建专用于加载数据的登录名和用户。 然后，将加载用户添加到启用相应最大内存分配的[资源类](resource-classes-for-workload-management.md)。

由于当前是以服务器管理员的身份连接的，因此可以创建登录名和用户。 使用以下步骤创建名为 LoaderRC20 的登录名和用户。 然后将该用户分配到 staticrc20 资源类。

1. 在 SSMS 中右键单击“master”，然后在显示的下拉菜单中选择“新建查询”。 此时将打开一个新的查询窗口。

    ![在 Master 中新建查询](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. 在查询窗口中，输入以下 T-SQL 命令，创建一个名为 LoaderRC20 的登录名和用户，并将“a123STRONGpassword!”替换为自己的密码。

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. 选择“执行”。

4. 右键单击“mySampleDataWarehouse”，然后选择“新建查询”。 此时会打开一个新的查询窗口。  

    ![针对示例数据仓库的新查询](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. 输入以下 T-SQL 命令，为 LoaderRC20 登录名创建名为 LoaderRC20 的数据库用户。 第二行为新用户授予对新数据仓库的 CONTROL 权限。  这些权限类似于使用户成为数据库所有者。 第三行将新用户添加为 staticrc20 [资源类](resource-classes-for-workload-management.md)的成员。

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. 选择“执行”。

## <a name="connect-to-the-server-as-the-loading-user"></a>以加载用户的身份连接到服务器

加载数据的第一步是以 LoaderRC20 的身份登录。  

1. 在对象资源管理器中选择“连接”下拉菜单，然后选择“数据库引擎”。 此时会显示“连接到服务器”对话框。

    ![使用新登录名连接](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. 输入完全限定的服务器名称，并输入“LoaderRC20”作为登录名。  输入 LoaderRC20 的密码。

3. 选择“连接” 。

4. 当连接准备就绪时，对象资源管理器中出现两个服务器连接。 一个是作为 ServerAdmin 连接，另一个是作为 MedRCLogin 连接。

    ![连接成功](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>为示例数据创建表

已准备好开始将数据加载到新的数据仓库。 本教程的此部分说明如何使用 COPY 语句从 Azure 存储 Blob 加载纽约市出租车数据。 若要了解如何将数据置于 Azure Blob 存储或直接从源加载数据以供将来参考，请参阅[加载概述](design-elt-data-loading.md)。

运行以下 SQL 脚本，并指定要加载的数据的相关信息。 此信息包括数据所在的位置、数据内容的格式以及数据的表定义。

1. 在前一节中，已经以 LoaderRC20 的身份登录数据仓库。 在 SSMS 中，右键单击 LoaderRC20 连接，然后选择“新建查询”。  此时会显示一个新的查询窗口。

    ![新的加载查询窗口](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. 比较查询窗口和之间的图像。  验证新的查询窗口以 LoaderRC20 的身份运行，并对 MySampleDataWarehouse 数据库执行查询。 使用此查询窗口执行所有加载步骤。

7. 运行以下 T-SQL 语句以创建表：

    ```sql
    CREATE TABLE [dbo].[Date]
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
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
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
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
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
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
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
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>将数据加载到数据仓库

本部分使用 [COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)从 Azure 存储 Blob 加载示例数据。  

> [!NOTE]
> 本教程直接将数据加载到最终表。 对于生产工作负载，通常要加载到临时表中。 数据在临时表中时，可以执行任何必要的转换。 

1. 运行以下语句以加载数据：

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. 在加载数据的同时查看数据。 假设要加载几个 GB 的数据，并将其压缩成高性能群集列存储索引。 运行以下使用动态管理视图 (DMV) 的查询以显示负载的状态。

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
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. 查看所有系统查询。

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. 结果让人欣慰，数据已顺利载入数据仓库。

    ![查看已加载的表](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>清理资源

需要为加载到数据仓库中的计算资源和数据付费。 这些需要单独计费。

* 如果想要将数据保留在存储中，可以在不使用数据仓库时暂停计算。 暂停计算后，仅需为数据存储付费，并且随时都可在准备处理数据时恢复计算。
* 如果不想支付将来的费用，则可以删除数据仓库。

请按照下列步骤按需清理资源。

1. 登录到 [Azure 门户](https://portal.azure.com)，选择你的数据仓库。

    ![清理资源](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. 若要暂停计算，请选择“暂停”按钮。 暂停数据仓库后，可看到“启动”按钮。  若要恢复计算，请选择“启动”。

3. 若要删除数据仓库，以便不再为计算或存储付费，请选择“删除”。

4. 若要删除创建的服务器，请在上一个映像中选择 **mynewserver-20180430.database.windows.net** ，然后选择 " **删除**"。  请审慎执行此操作，因为删除服务器会删除分配给该服务器的所有数据库。

5. 若要删除资源组，请选择“myResourceGroup”，然后选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，已学习了如何创建数据仓库以及用于加载数据的用户。 使用简单的 [COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples)将数据加载到数据仓库。

完成了以下操作：
> [!div class="checklist"]
>
> * 在 Azure 门户中创建数据仓库
> * 在 Azure 门户中设置服务器级防火墙规则
> * 使用 SSMS 连接到数据仓库
> * 创建了专用于加载数据的用户
> * 为示例数据创建表
> * 使用 COPY T-SQL 语句将数据加载到数据仓库
> * 查看了正在加载的数据的进度

转到开发概述，了解如何将现有数据库迁移到 Azure Synapse Analytics：

> [!div class="nextstepaction"]
> [将现有数据库迁移到 Azure Synapse Analytics 的设计决策](sql-data-warehouse-overview-develop.md)

有关加载示例和参考的详细信息，请查看以下文档：

- [COPY 语句参考文档](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [每个身份验证方法的 COPY 示例](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [单个表的 COPY 快速入门](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
