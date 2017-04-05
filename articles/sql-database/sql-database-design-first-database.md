---
title: "设计你的第一个 Azure SQL 数据库 | Microsoft Docs"
description: "了解如何设计你的第一个 Azure SQL 数据库。"
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/23/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 313bcf4fbc0ab7f251dd62b1e2151afef8392a55
ms.lasthandoff: 03/28/2017


---

# <a name="design-your-first-azure-sql-database"></a>设计你的第一个 Azure SQL 数据库

本教程将使用 Azure 门户在具有服务器级防火墙的新服务器上创建数据库。 然后，将使用 SQL Server Management Studio 创建表、将数据加载到该表中、查询该表并向表中添加索引。 最后，在添加新表之前，将使用 SQL 数据库服务的自动备份功能将数据库恢复到较早的时间点。

若要完成本教程，请确保已安装最新版的 [ SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) 

## <a name="step-1---log-in-to-the-azure-portal"></a>步骤 1 - 登录 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="step-2---create-a-sql-database"></a>步骤 2：创建 SQL 数据库

创建 Azure SQL 数据库时，会使用定义好的一组[计算和存储资源](sql-database-service-tiers.md)。 数据库在 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)和 [Azure SQL 数据库逻辑服务器](sql-database-features.md)中创建。 

请按以下步骤创建包含 Adventure Works LT 示例数据的 SQL 数据库。 

1. 单击 Azure 门户左上角的“新建”按钮。

2. 从“新建”页中选择“数据库”，然后从“数据库”页中选择“SQL 数据库”。

3. 使用必需信息填充 SQL 数据库窗体： 
   - 数据库名称：提供数据库名称
   - 订阅：选择订阅
   - 资源组：选择新的或现有的
   - 源：选择“示例(AdventureWorksLT)”
   - 服务器：创建一个新服务器（**服务器**名称必须全局唯一）
   - 弹性池：对于此快速入门，选择“现在不”
   - 定价层：选择“20 DTU”和 **250** GB 的存储
   - 排序规则：无法在导入示例数据库时更改此值 
   - 固定到仪表板：选择此复选框

      ![创建数据库](./media/sql-database-get-started/create-database-s1.png)

4. 完成后，单击“创建”。 预配需要数分钟。
5. SQL 数据库部署完成以后，在仪表板上选择“SQL 数据库”，或者从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的新数据库。 此时会打开数据库的概览页，显示完全限定的服务器名称（例如 **mynewserver20170313.database.windows.net**），并且会提供进行进一步配置所需的选项。

      ![new-sql 数据库](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="step-3---create-a-server-level-firewall-rule"></a>步骤 3 - 创建服务器级防火墙规则

SQL 数据库服务会创建一个防火墙，阻止外部应用程序和工具连接到服务器和数据库。 按照以下步骤为 IP 地址创建 [SQL 数据库服务器级防火墙规则](sql-database-firewall-configure.md)，以便通过 SQL 数据库防火墙进行外部连接。 

1. 单击数据库工具栏上的“设置服务器防火墙”。 此时会打开 SQL 数据库服务器的“防火墙设置”页。 

      ![服务器防火墙规则](./media/sql-database-get-started/server-firewall-rule.png) 

2. 在工具栏上单击“添加客户端 IP”，然后单击“保存”。 此时会针对当前的 IP 地址创建服务器级防火墙规则。

3. 单击“确定”，然后单击“X”关闭“防火墙设置”页。

现在可以使用 SQL Server Management Studio 或其他所选工具连接到数据库及其服务器。

## <a name="step-4---get-connection-information"></a>步骤 4 - 获取连接信息

请在 Azure 门户中获取 Azure SQL 数据库服务器的完全限定服务器名称。 请使用 SQL Server Management Studio 通过完全限定的服务器名称连接到服务器。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“Azure 门户”页的“概要”窗格中，找到并复制“服务器名称”。

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="step-5---connect-to-the-server-using-ssms"></a>步骤 5 - 使用 SSMS 连接服务器

使用 SQL Server Management Studio 建立到 Azure SQL 数据库服务器的连接。

1. 在 Windows 搜索框中键入 **SSMS**， 然后单击 **Enter** 打开 SSMS。

2. 在“连接到服务器”对话框中，输入以下信息：
   - **服务器类型**：指定数据库引擎
   - **服务器名称**：输入完全限定的服务器名称，例如 **mynewserver20170313.database.windows.net**
   - **身份验证**：指定 SQL Server 身份验证
   - **登录名**：输入服务器管理员帐户
   - **密码**：输入服务器管理员帐户的密码
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. 单击“连接”。 此时会在 SSMS 中打开“对象资源管理器”窗口。 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. 在对象资源管理器中展开“数据库”，然后展开 **mySampleDatabase**，查看示例数据库中的对象。

## <a name="step-6---create-and-query-a-table"></a>步骤 6 - 创建表并进行查询 
1. 在“对象资源管理器”中，右键单击“mySampleDatabase”，然后单击“新建查询”。 此时会打开一个空白查询窗口，该窗口连接到数据库。
2. 在查询窗口中执行以下查询：

   ```sql 
   CREATE TABLE [dbo].[Students]
   (
     [student_id] int, 
     [name] varchar(100),
     [age] int,
     [email] varchar(100),
     [AddressID] int REFERENCES [SalesLT].[Address] (AddressID)
   );
   ```

   查询完成后，就会在名为“学生”的数据库中创建一个空表。

3. 在 SSMS 查询窗口中执行以下查询： 

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   ```

   “学生”表未返回任何数据。

## <a name="step-7---load-data-into-the-table"></a>步骤 7 - 向表中加载数据 
1. 打开“命令提示符”窗口。

2. 执行以下 PowerShell 命令，将示例文本文件下载到当前目录。

   ```powershell
   powershell -command "& { (New-Object Net.WebClient).DownloadFile('https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData.txt', 'SampleStudentData.txt'); echo 'Download complete' }" 
   ``` 

3. 完成后，执行以下命令，将 1000 个行插入到“学生”表中，使用环境值替换 **ServerName**、**DatabaseName**、**UserName** 和 **Password** 的值。

   ```bcp
   bcp Students in SampleStudentData.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

现已将样本数据加载到了之前创建的表中。

## <a name="step-8---add-an-index-to-a-table"></a>步骤 8 – 向表中添加索引
若想更有效地搜索表中特定的值，请在“学生”表上创建一个索引。 索引以此种方式组织数据：必须查看所有数据以查找特定值。

1. 在 SSMS 查询窗口中执行以下查询：

   ```sql 
   CREATE NONCLUSTERED INDEX IX_Age ON Students (age);
   ```

2. 在 SSMS 查询窗口中执行以下查询：

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   WHERE age > 20
   ```

   此查询返回 20 岁以上的学生的姓名、年龄和电子邮件。

## <a name="step-9---restore-a-database-to-a-point-in-time"></a>步骤 9 - 将数据库还原到某个时间点 
Azure 中的数据库有[连续备份](sql-database-automated-backups.md)，每 5 - 10分钟自动执行一次。 这些备份可用于将数据库还原到以前的时间点。 将数据库还原到不同的时间点，在指定时间点（在服务层保留时间段内）原始数据库的服务器中创建一个备份数据库。 以下步骤将示例数据库还原到添加“学生”表之前的时间点。 

1. 在数据库的“SQL 数据库”页上，单击工具栏上的“还原”。 将打开“还原”页面。

    <img src="./media/sql-database-design-first-database/restore.png" alt="restore" style="width: 780px;" />

2. 使用必需信息填充“还原”窗体：
    * 数据库名称：提供数据库名称 
    * 时间点：选择“还原”窗体上的“时间点”选项卡 
    * 还原点：选择更改数据库前的时间
    * 目标服务器：还原数据库时不能更改此值 
    * 弹性数据库池：选择“无”  
    * 定价层：选择“20 个 DTU”和“250 GB”的存储。

    <img src="./media/sql-database-design-first-database/restore-point.png" alt="restore-point" style="width: 780px;" />

3. 单击“确定”，将数据库还原到添加“学生”表之前的时间点。

## <a name="next-steps"></a>后续步骤 
若要了解常见任务的 PowerShell 示例，请参阅 [SQL 数据库 PowerShell 示例](sql-database-powershell-samples.md)

