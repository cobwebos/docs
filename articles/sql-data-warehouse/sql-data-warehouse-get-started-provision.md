<properties
   pageTitle="在 Azure 门户中创建 SQL 数据仓库 | Azure"
   description="了解如何在 Azure 门户中创建 Azure SQL 数据仓库"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="06/20/2016"
   wacn.date=""/>

# 创建 Azure SQL 数据仓库

> [AZURE.SELECTOR]
- [Azure 门户](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

在本教程中，将使用 Azure 门户来创建包含 AdventureWorksDW 示例数据库的 SQL 数据仓库。


[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]


1. 登录到 [Azure 门户](https://manage.windowsazure.cn)。

2. 单击“+ 新建”>“数据 + 存储”>“SQL 数据仓库”。

    ![创建](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. 在“SQL 数据仓库”边栏选项卡中，填写所需的信息，然后按“创建”进行创建。

    ![创建数据库](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **服务器**：建议先选择服务器。可以选择现有的服务器，或[创建一个新的服务器](/documentation/articles/sql-data-warehouse-get-started-new-server)。 

	- **数据库名称**：将用于引用 SQL 数据仓库的名称。对服务器而言，它必须是唯一的。
	
    - **性能**：我们建议从 400 DWU 开始。可以将滑块向左或向右移动以调整数据仓库的性能，也可以在创建之后增加或减少。若要了解有关 DWU 的详细信息，请参阅我们有关[缩放](/documentation/articles/sql-data-warehouse-overview-scalability)的文档或者我们的[定价页面](/home/features/sql-data-warehouse/#price)。

    - **订阅**：选择此 SQL 数据仓库将会计费的订阅。

    - **资源组**：资源组是一种容器，旨在帮助你管理 Azure 资源集合。了解有关[资源组](/documentation/articles/resource-group-portal)的详细信息。

    - **选择源**：单击“选择源”>“示例”。由于此时只有一个可用的示例数据库，因此当你选择“示例”时，Azure 将在“选择示例”选项中自动填充 AdventureWorksDW。

4. 单击“创建”以创建 SQL 数据仓库。

5. 等待几分钟，SQL 数据仓库将准备就绪。完成后，将返回到 [Azure 门户](https://manage.windowsazure.cn)。你可以在仪表板上找到你的 SQL 数据仓库，它列于 SQL 数据库之下，或在用来创建它的资源组中。

    ![门户视图](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL 数据库创建服务器](../includes/sql-database-create-new-server-firewall-portal.md)]

## 后续步骤

你已经创建 SQL 数据仓库，现在可以[连接](/documentation/articles/sql-data-warehouse-get-started-connect)并开始查询。

若要将数据加载到 SQL 数据仓库中，请参阅[加载概述](/documentation/articles/sql-data-warehouse-overview-load)。

如果尝试将现有数据库迁移到 SQL 数据仓库，请参阅[迁移概述](/documentation/articles/sql-data-warehouse-overview-migrate)或使用[迁移实用工具](/documentation/articles/sql-data-warehouse-migrate-migration-utility)。


<!---HONumber=Mooncake_0704_2016-->