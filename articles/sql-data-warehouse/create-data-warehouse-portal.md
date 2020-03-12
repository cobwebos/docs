---
title: 创建和查询 Synapse SQL 池（Azure 门户）
description: 使用 Azure 门户创建并查询 Synapse SQL 池
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 6966932e95ff538de4b2f9be1ac06516311a0919
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128725"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建并查询 Synapse SQL 池

使用 Azure 门户在 Azure Synapse Analytics （以前称为 SQL DW）中快速创建和查询 Synapse SQL 池（数据仓库）。

## <a name="prerequisites"></a>必备条件

1. 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

   > [!NOTE]
   > 在 Azure Synapse 中创建 SQL 池可能会导致新的计费服务。 有关详细信息，请参阅[Azure Synapse Analytics 定价](https://azure.microsoft.com/pricing/details/synapse-analytics/)。

2. 下载并安装最新版本的 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-sql-pool"></a>创建 SQL 池

使用 Azure Synapse Analytics 中的 SQL 池创建数据仓库。 SQL 池是使用一组定义的[计算资源](memory-concurrency-limits.md)创建的。 数据库在 [Azure 资源组](../azure-resource-manager/management/overview.md)和 [Azure SQL 逻辑服务器](../sql-database/sql-database-servers.md)中创建。

按照以下步骤创建包含**AdventureWorksDW**示例数据的 SQL 池。

1. 选择 Azure 门户的左上角的 "**创建资源**"。

   ![在 Azure 门户中创建资源](media/create-data-warehouse-portal/create-a-resource.png)

2. 选择**新**页上的 "**数据库**"，并在**功能**列表中选择 " **AZURE Synapse Analytics （以前称为 SQL DW）** "。

   ![创建空的数据仓库](media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. 在 "**基本**信息" 中，提供订阅、资源组、SQL 池名称和服务器名称：

   | 设置 | 建议的值 | 说明 |
   | :------ | :-------------- | :---------- |
   | **订阅** | 订阅 | 有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   | **资源组** | myResourceGroup | 如需有效的资源组名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming)（命名规则和限制）。 |
   | **SQL 池名称** | 任何全局唯一名称（例如*mySampleDataWarehouse*） | 如需有效的数据库名称，请参阅 [Database Identifiers](/sql/relational-databases/databases/database-identifiers)（数据库标识符）。 请注意，SQL 池是一种类型的数据库。 |
   | **Server** | 任何全局唯一名称 | 选择 "现有服务器" 或 "创建新的服务器名称"，选择 "**新建**"。 如需有效的服务器名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming)（命名规则和限制）。 |

   ![创建数据仓库基本详细信息](media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. 在 "**性能级别**" 下，选择 "**选择性能级别**" 以选择使用滑块更改配置。

   ![更改数据仓库性能级别](media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   有关性能级别的详细信息，请参阅[在 AZURE SQL 数据仓库中管理计算](sql-data-warehouse-manage-compute-overview.md)。

5. 现在，你已完成 Azure Synapse 分析表单的 "基本信息" 选项卡，请选择 "**查看 + 创建**"，然后选择 "**创建**" 以创建 SQL 池。 预配需要数分钟。

   ![选择 "查看 + 创建"](media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![选择“创建”](media/create-data-warehouse-portal/create-sql-pool-create.png)

6. 在工具栏上，选择 "**通知**" 可监视部署过程。

   ![通知](media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>创建服务器级防火墙规则

Azure Synapse 服务在服务器级别创建防火墙。 此防火墙会阻止外部应用程序和工具连接到服务器或服务器上的任何数据库。 要启用连接，可以添加防火墙规则，为特定 IP 地址启用连接。 按照以下步骤为客户端的 IP 地址创建[服务器级防火墙规则](../sql-database/sql-database-firewall-configure.md)。

> [!NOTE]
> Azure Synapse 通过端口1433进行通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到 Azure SQL 数据库服务器，除非 IT 部门打开了端口 1433。

1. 部署完成后，从左侧菜单中选择“所有服务”。 选择 "**数据库**"，选择 " **azure Synapse analytics** " 旁边的星号，将 azure Synapse analytics 添加到收藏夹。

2. 从左侧菜单中选择 " **Azure Synapse Analytics** "，并选择 " **azure Synapse 分析**" 页上的 " **mySampleDataWarehouse** "。 此时会打开数据库的 "概述" 页，其中显示了完全限定的服务器名称（例如**sqlpoolservername.database.windows.net**），并提供了进一步配置的选项。

3. 复制此完全限定的服务器名称，以便在此快速入门和其他快速入门中使用它连接到服务器及其数据库。 若要打开服务器设置，请选择服务器名称。

   ![查找服务器名称](media/create-data-warehouse-portal/find-server-name.png)

4. 选择 "**显示防火墙设置**"。

   ![服务器设置](media/create-data-warehouse-portal/server-settings.png)

5. 此时会打开 SQL 数据库服务器的“防火墙设置”页。

   ![服务器防火墙规则](media/create-data-warehouse-portal/server-firewall-rule.png)

6. 若要将当前的 IP 地址添加到新的防火墙规则，请在工具栏上选择 "**添加客户端 IP** "。 防火墙规则可以针对单个 IP 地址或一系列 IP 地址打开端口 1433。

7. 选择“保存”。 此时会针对当前的 IP 地址创建服务器级防火墙规则，在逻辑服务器上打开 端口 1433。

8. 选择 **"确定"** ，然后关闭 "**防火墙设置**" 页。

你现在可以使用此 IP 地址连接到 SQL server 及其 SQL 池。 可从 SQL Server Management Studio 或另一种所选工具进行连接。 连接时，请使用之前创建的 ServerAdmin 帐户。

> [!IMPORTANT]
> 默认情况下，所有 Azure 服务都允许通过 SQL 数据库防火墙进行访问。 在此页上选择 "**关闭**"，然后选择 "**保存**"，为所有 Azure 服务禁用防火墙。

## <a name="get-the-fully-qualified-server-name"></a>获取完全限定的服务器名称

请在 Azure 门户中获取 SQL Server 的完全限定的服务器名称。 稍后，在连接到服务器时，将使用该完全限定的名称。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 从左侧菜单中选择 " **Azure Synapse Analytics** "，并在**Azure Synapse Analytics**页上选择。

3. 在数据库的“Azure 门户”页的“概要”窗格中，找到并复制“服务器名称”。 在此示例中，完全限定名为 sqlpoolservername.database.windows.net。

    ![连接信息](media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>以服务器管理员的身份连接到服务器

本部分使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) 来建立与 Azure SQL Server 的连接。

1. 打开 SQL Server Management Studio。

2. 在“连接到服务器”对话框中，输入以下信息：

   | 设置 | 建议的值 | 说明 |
   | :------ | :-------------- | :---------- |
   | 服务器类型 | 数据库引擎 | 此值是必需的 |
   | 服务器名称 | 完全限定的服务器名称 | 下面是一个示例： **sqlpoolservername.database.windows.net**。 |
   | Authentication | SQL Server 身份验证 | SQL 身份验证是本教程中配置的唯一身份验证类型。 |
   | 登录 | 服务器管理员帐户 | 创建服务器时指定的帐户。 |
   | 密码 | 服务器管理员帐户的密码 | 创建服务器时指定的密码。 |
   ||||

   ![连接到服务器](media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. 选择“连接”。 对象资源管理器窗口在 SSMS 中打开。 

4. 在“对象资源管理器”中，展开“数据库”。 然后展开“mySampleDatabase”，查看新数据库中的对象。

   ![数据库对象](media/create-data-warehouse-portal/connected-ssms.png) 

## <a name="run-some-queries"></a>运行一些查询

SQL 数据仓库使用 T-SQL 作为查询语言。 打开一个查询窗口并运行一些 T-SQL 查询，请使用以下步骤：

1. 右键选择**mySampleDataWarehouse** ，然后选择 "**新建查询**"。 “新建查询”窗口随即打开。

2. 在查询窗口中，输入以下命令以查看数据库列表。

    ```sql
    SELECT * FROM sys.databases
    ```

3. 选择 "**执行**"。 查询结果显示两个数据库：master 和 mySampleDataWarehouse。

   ![查询数据库](media/create-data-warehouse-portal/query-databases.png)

4. 若要查看某些数据，请使用以下命令查看姓氏为 Adams 家中有三个孩子的客户数。 结果列出六个客户。 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![查询 dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>清理资源

需要为数据仓库单位和存储 SQL 池的数据收费。 这些计算和存储资源是分开计费的。

- 如果要将数据保留在存储中，则可以在不使用 SQL 池时暂停计算。 如果暂停计算资源，则你只需支付数据存储费用。 只要准备好处理数据，便可以恢复计算。

- 如果要删除未来的费用，可以删除 SQL 池。

按照以下步骤清理不再需要的资源。

1. 登录到[Azure 门户](https://portal.azure.com)，选择你的 SQL 池。

   ![清理资源](media/create-data-warehouse-portal/clean-up-resources.png)

2. 若要暂停计算，请选择“暂停”按钮。 暂停 SQL 池后，会看到 "**继续**" 按钮。 若要恢复计算，请选择 "**恢复**"。

3. 若要删除 SQL 池以便不为计算或存储付费，请选择 "**删除**"。

4. 若要删除创建的 SQL server，请在上一个映像中选择**sqlpoolservername.database.windows.net** ，然后选择 "**删除**"。 请谨慎执行此删除操作，因为删除服务器的同时也会删除分配给该服务器的所有数据库。

5. 若要删除资源组，请选择“myResourceGroup”，然后选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

若要了解有关将数据加载到 SQL 池中的详细信息，请继续转到将[数据加载到 sql 池](load-data-from-azure-blob-storage-using-polybase.md)一文。 
