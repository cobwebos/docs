---
title: 使用 BI 分析工具连接到 Azure Cosmos DB | Microsoft Docs
description: 了解如何使用 Azure Cosmos DB ODBC 驱动程序创建表和视图，以便能够在 BI 和数据分析软件中查看规范化数据。
keywords: odbc, odbc 驱动程序
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: e93b241c79a50380f4ef1538dfbf7615232e6c49
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096503"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>通过 ODBC 驱动程序使用 BI 分析工具连接到 Azure Cosmos DB

借助 Azure Cosmos DB ODBC 驱动程序，可以使用 SQL Server Integration Services、Power BI Desktop 和 Tableau 等 BI 分析工具连接到 Azure Cosmos DB，以便能够在这些解决方案中分析和创建 Azure Cosmos DB 数据的可视化效果。

Azure Cosmos DB ODBC 驱动程序符合 ODBC 3.8 规范，支持 ANSI SQL-92 语法。 该驱动程序提供丰富的功能来帮助用户重新规范化 Azure Cosmos DB 中的数据。 使用该驱动程序，能够以表和视图的形式表示 Azure Cosmos DB 中的数据。 使用该驱动程序还可以针对表和视图执行 SQL 操作，包括 group by 查询、插入、更新和删除。

## <a name="why-do-i-need-to-normalize-my-data"></a>为何需要将数据规范化？
Azure Cosmos DB 是一种无架构数据库，它允许应用程序即时迭代其数据模型，而不会将它们限定于一个严格定义的架构，因此可以加快应用的开发。 一个 Azure Cosmos DB 数据库可以包含各种结构的 JSON 文档。 这非常适合于快速应用程序开发，但如果用户想要使用数据分析和 BI 工具来分析和创建数据报表，则数据通常需要平整化并遵守特定的架构。

这就是 ODBC 驱动程序的作用所在。 通过使用 ODBC 驱动程序，可以根据数据分析和报告需求将 Azure Cosmos DB 中的数据重新规范化为表和视图。 重新规范化的架构不会对基础数据造成影响，不强制要求开发人员遵守，可方便用户利用符合 ODBC 规范的工具来访问数据。 因此，Azure Cosmos DB 数据库不仅是开发团队的最爱，数据分析师也对它青睐有加。

现在，让我们学习 ODBC 驱动程序的入门知识。

## <a id="install"></a>步骤 1：安装 Azure Cosmos DB ODBC 驱动程序

1. 下载适用于环境的驱动程序：

    | 安装程序 | 支持的操作系统| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64)（适用于 64 位 Windows）| 64 位的 Windows 8.1 或更高版本、Windows 8、Windows 7、Windows Server 2012 R2、Windows Server 2012 和 Windows Server 2008 R2。| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/documentdb-odbc-32x64)（适用于 32 位或 64 位 Windows）| 64 位的 Windows 8.1 或更高版本、Windows 8、Windows 7、Windows XP、Windows Vista、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 和 Windows Server 2003。| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32)（适用于 32 位 Windows）|32 位的 Windows 8.1 或更高版本、Windows 8、Windows 7、Windows XP 和 Windows Vista。|

    在本地运行 msi 文件，启动“Microsoft Azure Cosmos DB ODBC 驱动程序安装向导”。 
2. 使用默认输入完成安装向导，安装 ODBC 驱动程序。
3. 在 Windows 搜索框中键入“ODBC 数据源”，在计算机上打开“ODBC 数据源管理器”应用。 
    单击“驱动程序”选项卡并检查“Microsoft Azure Cosmos DB ODBC 驱动程序”是否已列出，可确认是否已安装该驱动程序。

    ![Azure Cosmos DB ODBC 数据源管理器](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>步骤 2：连接到 Azure Cosmos DB 数据库

1. [安装 Azure Cosmos DB ODBC 驱动程序](#install)后，请在“ODBC 数据源管理器”窗口中单击“添加”。 可以创建一个用户 DSN 或系统 DSN。 在本示例中，将创建一个用户 DSN。
2. 在“创建新数据源”窗口中选择“Microsoft Azure Cosmos DB ODBC 驱动程序”，并单击“完成”。
3. 在“Azure Cosmos DB ODBC 驱动程序 SDN 设置”窗口中填写以下信息： 

    ![“Azure Cosmos DB ODBC 驱动程序 DSN 设置”窗口](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **数据源名称**：ODBC DSN 的友好名称。 此名称是 Azure Cosmos DB 帐户的唯一名称，因此，如果有多个帐户，请适当地为数据源命名。
    - **说明**：数据源的简短说明。
    - 主机：Azure Cosmos DB 帐户的 URI。 可在 Azure 门户的“Azure Cosmos DB 密钥”页中检索此信息，如以下屏幕截图所示。 
    - **访问密钥**：从 Azure 门户中“Azure Cosmos DB 密钥”页获取的主要或辅助读写或只读密钥，如以下屏幕截图所示。 如果 DSN 用于只读数据的处理和报告，我们建议使用只读密钥。
    ![“Azure Cosmos DB 密钥”页](./media/odbc-driver/odbc-driver-keys.png)
    - **加密以下对象的访问密钥**：根据此计算机的用户选择最合适的选项。 
4. 单击“测试”按钮，确保可以连接到 Azure Cosmos DB 帐户。 
5. 单击“高级选项”并设置以下值：
    - **查询一致性**：选择操作的[一致性级别](consistency-levels.md)。 默认值为“会话”。
    - **重试次数**：输入当初始请求因服务速率限制而未能完成时，重试操作的次数。
    - **架构文件**：此处有多个选项。
        - 默认情况下，可将此项保留原样（空白），驱动程序会在第一页的数据中扫描所有集合，确定每个集合的架构。 这称为“集合映射”。 如果未定义架构文件，驱动程序必须针对每个驱动程序会话执行扫描，这可能会导致使用 DSN 启动应用程序时需要更长的时间。 我们建议始终关联 DSN 的架构文件。
        - 如果已有一个架构文件（也许是使用[架构编辑器](#schema-editor)创建的文件），则可以单击“浏览”，导航到该文件，单击“保存”，并单击“确定”。
        - 如果想要创建新架构，请单击“确定”，并在主窗口中单击“架构编辑器”。 接下来，继续在[架构编辑器](#schema-editor)中提供信息。 创建新架构文件后，请记得返回“高级选项”窗口以包含新建的架构文件。

6. 完成并关闭“Azure Cosmos DB ODBC 驱动程序 DSN 设置”窗口后，新的用户 DSN 将添加到“用户 DSN”选项卡。

    ![“用户 DSN”选项卡中的新 Azure Cosmos DB ODBC DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>步骤 3：使用集合映射方法创建架构定义

可以使用两种类型的采样方法：**集合映射**或**表分隔符**。 采样会话可以利用这两种采样方法，但每个集合只能使用特定的采样方法。 以下步骤使用集合映射方法为一个或多个集合中的数据创建架构。 此采样方法将检索集合页面中的数据，以确定数据的结构。 它会将集合转置到 ODBC 端的某个表。 如果集合中的数据是同构的，此采样方法将十分快速高效。 如果集合包含异构类型的数据，我们建议使用[表分隔符映射方法](#table-mapping)，因为这是确定集合中数据结构的更可靠采样方法。 

1. 完成[连接到 Azure Cosmos DB 数据库](#connect)中的步骤 1-4 后，请在“Azure Cosmos DB ODBC 驱动程序 DSN 设置”窗口中单击“架构编辑器”。

    ![“Azure Cosmos DB ODBC 驱动程序 DSN 设置”窗口中的架构编辑器按钮](./media/odbc-driver/odbc-driver-schema-editor.png)
2. 在“架构编辑器”窗口中单击“新建”。
    “生成架构”窗口会显示 Azure Cosmos DB 帐户中的所有集合。 
3. 选择要采样的一个或多个集合，并单击“采样”。 
4. “设计视图”选项卡中显示了数据库、架构和表。 在表视图中，扫描会显示与列名称（“SQL 名称”、“源名称”等）关联的属性集。
    可以修改每个列，包括“SQL 名称”、“SQL 类型”、“SQL 长度”（如果适用）、“小数位数”（如果适用）、“精度”（如果适用）和“可为 Null”。
    - 如果想要从查询结果中排除某个列，可将对应的“隐藏列”设置为 **true**。 标记为“隐藏列 = true”的列不会返回供选择和投影，不过它们仍是架构的一部分。 例如，可以隐藏以“_”开头的所有 Azure Cosmos DB 系统必需属性。
    - “Id”列是唯一不能隐藏的字段，因为它用作规范化架构中的主键。 
5. 完成定义架构后，请单击“文件” | “保存”，导航到用于保存该架构的目录，并单击“保存”。

    以后如果想要配合 DSN 使用此架构，请通过“ODBC 数据源管理器”打开“Azure Cosmos DB ODBC 驱动程序 DSN 设置”窗口，单击“高级选项”，并在“架构文件”框中导航到保存的架构。 将架构文件保存到现有 DSN 会将 DSN 连接范围修改为架构定义的数据和结构。

## <a id="table-mapping"></a>步骤 4：使用表分隔符映射方法创建架构定义

可以使用两种类型的采样方法：**集合映射**或**表分隔符**。 采样会话可以利用这两种采样方法，但每个集合只能使用特定的采样方法。 

以下步骤使用**表分隔符**映射方法为一个或多个集合中的数据创建架构。 如果集合包含异构类型的数据，我们建议使用此采样方法。 可以使用此方法将采样范围限定为一组属性及其相应值。 例如，如果某个文档包含“Type”属性，可将采样范围限定为此属性的值。 采样的最终结果是针对每个指定的 Type 值返回一组表。 例如，Type = Car 将生成 Car 表，Type = Plane 将生成 Plane 表。

1. 完成[连接到 Azure Cosmos DB 数据库](#connect)中的步骤 1-4 后，请在“Azure Cosmos DB ODBC 驱动程序 DSN 设置”窗口中单击“架构编辑器”。
2. 在“架构编辑器”窗口中单击“新建”。
    “生成架构”窗口会显示 Azure Cosmos DB 帐户中的所有集合。 
3. 在“采样视图”选项卡上的集合“映射定义”列中选择一个集合，并单击“编辑”。 在“映射定义”窗口中，选择“表分隔符”方法。 然后执行以下操作：

    a. 在“属性”框中，键入分隔符属性的名称。 这是文档中要将采样范围限定到的属性（例如 City）。然后按 Enter 键。 

    b. 如果只想要将采样范围限定为刚刚输入的属性的值，请在选择框中选择该属性，在“值”框中输入值（例如 Seattle），然后按 Enter 键。 可以继续添加属性的多个值。 只需确保在输入值时选择正确的属性。

    例如，如果包含 City 的 **Attributes** 值，并想要将表限制为仅包含带有 New York 和 Dubai 城市值的行，请在“属性”框中输入 City，在“值”框中输入 New York 和 Dubai。
4. 单击“确定”。 
5. 完成想要采样的集合的映射定义后，请在“架构编辑器”窗口中单击“采样”。
     可以修改每个列，包括“SQL 名称”、“SQL 类型”、“SQL 长度”（如果适用）、“小数位数”（如果适用）、“精度”（如果适用）和“可为 Null”。
    - 如果想要从查询结果中排除某个列，可将对应的“隐藏列”设置为 **true**。 标记为“隐藏列 = true”的列不会返回供选择和投影，不过它们仍是架构的一部分。 例如，可以隐藏以 `_` 开头的所有 Azure Cosmos DB 系统必需属性。
    - “Id”列是唯一不能隐藏的字段，因为它用作规范化架构中的主键。 
6. 完成定义架构后，请单击“文件” | “保存”，导航到用于保存该架构的目录，并单击“保存”。
7. 返回“Azure Cosmos DB ODBC 驱动程序 DSN 设置”窗口，单击“高级选项”。 然后，在“架构文件”框中，导航到保存的架构文件并单击“确定”。 再次单击“确定”保存 DSN。 这会将创建的架构保存到 DSN。 

## <a name="optional-set-up-linked-server-connection"></a>（可选）设置链接服务器连接

通过设置链接服务器连接，可以从 SQL Server Management Studio (SSMS) 查询 Azure Cosmos DB。

1. 按[步骤 2](#connect) 中所述创建系统数据源并为其命名，如 `SDS Name`。
2. [安装 SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 并连接到服务器。 
3. 在 SSMS 查询编辑器中，使用以下命令为数据源创建链接服务器对象 `DEMOCOSMOS`。 将 `DEMOCOSMOS` 替换为链接服务器的名称，并将 `SDS Name` 替换为系统数据源的名称。

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
要查看新的链接服务器名称，请刷新链接服务器列表。

![SSMS 中的链接服务器](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>查询链接的数据库

若要查询链接的数据库，请输入 SSMS 查询。 在此示例中，查询从名为 `customers` 的集合的表中选择：

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

执行查询。 结果应如下所示：

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> 链接的 Cosmos DB 服务器不支持由四部分组成的命名。 将返回类似于以下消息的错误：

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>（可选）创建视图
可在采样过程中定义和创建视图。 这些视图相当于 SQL 视图。 它们是只读的，并且是定义的 Azure Cosmos DB SQL 的选择和投影范围。 

若要创建数据的视图，请在“架构编辑器”窗口中的“视图定义”列内，单击要采样的集合行中的“添加”。 然后在“视图定义”窗口中执行以下操作：
1. 单击“新建”，输入视图的名称（例如 EmployeesfromSeattleView），并单击“确定”。
2. 在“编辑视图”窗口中，输入一个 Azure Cosmos DB 查询。 输入内容必须是 Azure Cosmos DB SQL 查询，例如 `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`。并单击“确定”。

可以创建任意数量的视图。 定义完视图后，可以采样数据。 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>步骤 5：在 Power BI Desktop 等 BI 工具中查看数据

可以使用新 DSN 将 DocumentADB 连接到任何符合 ODBC 规范的工具 - 本步骤仅说明如何连接到 Power BI Desktop 并创建 Power BI 可视化效果。

1. 打开 Power BI Desktop。
2. 单击“获取数据”。
3. 在“获取数据”窗口中，单击“其他” | “ODBC” | “连接”。
4. 在“从 ODBC”窗口中，选择创建的数据源名称，并单击“确定”。 可将“高级选项”项保留空白。
5. 在“使用 ODBC 驱动程序访问数据源”窗口中，选择“默认或自定义”，并单击“连接”。 不需要包括“凭据连接字符串属性”。
6. 在“导航器”窗口的左窗格中，展开数据库和架构，并选择表。 结果窗格包含使用创建的架构的数据。
7. 要在 Power BI Desktop 中可视化数据，请选中表名称前面的框，并单击“加载”。
8. 在 Power BI Desktop 中的最左侧，选择“数据”选项卡 ![Power BI Desktop 中的“数据”选项卡](./media/odbc-driver/odbc-driver-data-tab.png) 确认数据是否已导入。
9. 现在，可以使用 Power BI 创建视觉对象：单击“报表”选项卡![Power BI Desktop 中的“报表”选项卡](./media/odbc-driver/odbc-driver-report-tab.png)，单击“新建视觉对象”，并自定义磁贴。 有关在 Power BI Desktop 中创建可视化效果的详细信息，请参阅 [Power BI 中的可视化效果类型](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/)。

## <a name="troubleshooting"></a>故障排除

如果遇到以下错误，请确保执行[步骤 2](#connect) 时在 Azure 门户中复制的“主机”和“访问密钥”值正确，并重试。 在 Azure 门户中使用“主机”和“访问密钥”值右侧的复制按钮可以正确无误地复制这些值。

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure Cosmos DB 的详细信息，请参阅[欢迎使用 Azure Cosmos DB](introduction.md)。
