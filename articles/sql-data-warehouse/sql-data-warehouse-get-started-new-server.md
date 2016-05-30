<properties
   pageTitle="在 Azure 门户中创建 SQL 数据仓库数据库 | Microsoft Azure"
   description="了解如何在 Azure 门户中创建 Azure SQL 数据仓库"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="05/03/2016"
   wacn.date=""/>

# 创建新的逻辑服务器

在 SQL 数据库和 SQL 数据仓库中，每个数据库将分配到一个服务器，每个服务器将分配到一个地理位置。该服务器称为逻辑 SQL 服务器。

> [AZURE.NOTE] <a name="note"></a>逻辑 SQL 服务器：
  >
  > + 提供了在同一地理位置内配置多个数据库的一致方法。
  > + 不是像在本地服务器上那样的物理硬件。它是服务软件的一部分。这就是为什么我们称之为*逻辑服务器*。
  > + 可以承载多个数据库，而不会影响它们的性能。
  > + 在其名称中使用小写的 *s*。SQL 服务器 (SQL **s**erver) 是 Azure 逻辑服务器，而 SQL **S**erver 则是 Microsoft 的本地数据库产品。

1. 单击“服务器”>“创建新服务器”。该服务器不收取费用。如果你已有想要使用的 V12 逻辑 SQL 服务器，请选择现有的服务器，然后转到下一步骤。

    ![创建新服务器](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. 填入**新服务器**信息。

	- **服务器名称**：输入逻辑服务器的名称。这对于各个地理位置而言都是唯一的。
	- **服务器管理员名称**：输入服务器管理员帐户的用户名。
	- **密码**：输入服务器管理员密码。
	- **位置**：选择服务器的地理位置。若要减少数据传输时间，最好将服务器的地理位置定位为靠近此数据库将要访问的其他数据资源。
	- **创建 V12 服务器**：“是”为 SQL 数据仓库的唯一选项。
	- **允许 Azure 服务访问服务器**：对于 SQL 数据仓库始终会选中此选项

    >[AZURE.NOTE] 请务必将服务器名称、服务器管理员名称和密码存储在某个位置。以后需要使用此信息登录服务器。

3. 单击“确定”以存储逻辑 SQL 服务器配置设置并返回“SQL 数据仓库”边栏选项卡。

    ![配置新服务器](./media/sql-data-warehouse-get-started-provision/configure-server.png)

<!---HONumber=Mooncake_0523_2016-->