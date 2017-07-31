---
title: "设计你的第一个 Azure SQL 数据库 | Microsoft Docs"
description: "了解如何设计你的第一个 Azure SQL 数据库。"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/20/2017
ms.author: janeng
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: d5e63e7079b652e69a089aef495952d29cae67a2
ms.contentlocale: zh-cn
ms.lasthandoff: 07/19/2017

---

# <a name="design-your-first-azure-sql-database"></a>设计你的第一个 Azure SQL 数据库

Azure SQL 数据库与 Microsoft 云（“Azure”）中的数据库即服务 (DBaaS) 相关。 在本教程中，了解如何使用 Azure 门户 SQL 和 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) 执行以下操作： 

> [!div class="checklist"]
> * 在 Azure 门户中创建数据库
> * 在 Azure 门户中设置服务器级防火墙规则
> * 使用 SSMS 连接到数据库
> * 使用 SSMS 创建表
> * 使用 BCP 大容量加载数据
> * 使用 SSMS 查询这些数据
> * 在 Azure 门户中将数据库还原到之前的[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保已安装：
- 最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)。
- 最新版本的 [BCP 和 SQLCMD][https://www.microsoft.com/download/details.aspx?id=36433]。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-blank-sql-database-in-the-azure-portal"></a>在 Azure 门户中创建空白 SQL 数据库

创建 Azure SQL 数据库时，会使用定义好的一组[计算和存储资源](sql-database-service-tiers.md)。 数据库在 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)和 [Azure SQL 数据库逻辑服务器](sql-database-features.md)中创建。 

按照以下步骤创建空的 SQL 数据库。 

1. 单击 Azure 门户左上角的“新建”按钮。

2. 从“新建”页中选择“数据库”，然后从“数据库”页中选择“SQL 数据库”。 

   ![创建空数据库](./media/sql-database-design-first-database/create-empty-database.png)

3. 如上图所示，在“SQL 数据库”窗体中填写以下信息：   

   | 设置       | 建议的值 | 说明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **数据库名称** | mySampleDatabase | 如需有效的数据库名称，请参阅 [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)（数据库标识符）。 | 
   | **订阅** | 你的订阅  | 有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   | **资源组** | myResourceGroup | 如需有效的资源组名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
   | **选择源** | 空白数据库 | 指定创建空白数据库。 |

4. 单击“服务器”，为新数据库创建并配置新服务器。 使用以下信息填写“新建服务器”窗体： 

   | 设置       | 建议的值 | 说明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **服务器名称** | 任何全局唯一名称 | 如需有效的服务器名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 | 
   | 服务器管理员登录名 | 任何有效的名称 | 如需有效的登录名，请参阅 [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)（数据库标识符）。|
   | **密码** | 任何有效的密码 | 密码必须至少有 8 个字符，且必须包含以下类别中的三个类别的字符：大写字符、小写字符、数字以及非字母数字字符。 |
   | **位置** | 任何有效的位置 | 有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。 |

   ![创建数据库 - 服务器](./media//sql-database-design-first-database/create-database-server.png)

5. 单击“选择”。

6. 单击“定价层”为新数据库指定服务层和性能级别。 对于本教程，请选择 **20 DTU** 和 **250** GB 存储。

   ![创建数据库 - s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. 单击“应用” 。  

8. 选择空白数据库的“排序规则”（就本教程来说，请使用默认值）。 有关排序规则的详细信息，请参阅 [Collations](https://docs.microsoft.com/sql/t-sql/statements/collations)（排序规则）

9. 单击“创建”预配数据库。 大约需要一分半的时间才能完成预配。 

10. 在工具栏上，单击“通知”可监视部署过程。

   ![通知](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 门户中创建服务器级防火墙规则

SQL 数据库服务在服务器级别创建一个防火墙。除非创建了防火墙规则来为特定的 IP 地址打开防火墙，否则会阻止外部应用程序和工具连接到服务器或服务器上的任何数据库。 按照以下步骤为客户端 IP 地址创建 [SQL 数据库服务器级防火墙规则](sql-database-firewall-configure.md)，并只允许通过针对你的 IP 地址打开的 SQL 数据库防火墙建立外部连接。 

> [!NOTE]
> 通过端口 1433 进行的 SQL 数据库通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到 Azure SQL 数据库服务器，除非 IT 部门打开了端口 1433。
>

1. 部署完成后，在左侧菜单中单击“SQL 数据库”，然后在“SQL 数据库”页上单击“mySampleDatabase”。 此时会打开数据库的概览页，显示完全限定的服务器名称（例如 mynewserver20170313.database.windows.net），并且会提供进行进一步配置所需的选项。 请复制此完全限定的服务器名称，供以后使用。

   > [!IMPORTANT]
   > 在后续的快速入门中，需提供此完全限定的服务器名称才能连接到服务器及其数据库。
   > 

   ![服务器名称](./media/sql-database-connect-query-dotnet/server-name.png) 

2. 如上图所示，在工具栏上单击“设置服务器防火墙”。 此时会打开 SQL 数据库服务器的“防火墙设置”页。 

   ![服务器防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule.png) 


3. 在工具栏上单击“添加客户端 IP”，将当前的 IP 地址添加到新的防火墙规则。 防火墙规则可以针对单个 IP 地址或一系列 IP 地址打开端口 1433。

4. 单击“保存” 。 此时会针对当前的 IP 地址创建服务器级防火墙规则，在逻辑服务器上打开 端口 1433。

   ![设置服务器防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. 单击“确定”，然后关闭“防火墙设置”页。

现在可以使用之前创建的服务器管理员帐户通过 SQL Server Management Studio 或其他所选工具从此 IP 地址连接到 SQL 数据库服务器及其数据库。

> [!IMPORTANT]
> 默认情况下，所有 Azure 服务都允许通过 SQL 数据库防火墙进行访问。 在此页上单击“关”即可对所有 Azure 服务执行禁用操作。

## <a name="sql-server-connection-information-in-the-azure-portal"></a>Azure 门户中的 SQL Server 连接信息

请在 Azure 门户中获取 Azure SQL 数据库服务器的完全限定服务器名称。 请使用 SQL Server Management Studio 通过完全限定的服务器名称连接到服务器。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“Azure 门户”页的“概要”窗格中，找到并复制“服务器名称”。

   ![连接信息](./media/sql-database-connect-query-dotnet/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>使用 SSMS 连接到数据库

使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) 建立到 Azure SQL 数据库服务器的连接。

1. 打开 SQL Server Management Studio。

2. 在“连接到服务器”对话框中，输入以下信息：

   | 设置       | 建议的值 | 说明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | 服务器类型 | 数据库引擎 | 此值是必需的 |
   | 服务器名称 | 完全限定的服务器名称 | 该名称应类似于：mynewserver20170313.database.windows.net。 |
   | 身份验证 | SQL Server 身份验证 | SQL 身份验证是本教程中配置的唯一身份验证类型。 |
   | 登录 | 服务器管理员帐户 | 这是在创建服务器时指定的帐户。 |
   | 密码 | 服务器管理员帐户的密码 | 这是在创建服务器时指定的密码。 |

   ![连接到服务器](./media/sql-database-connect-query-ssms/connect.png)

3. 单击“连接到服务器”对话框中的“选项”。 在“连接到数据库”部分输入 **mySampleDatabase**，以连接到此数据库。

   ![连接到服务器上的 DB](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. 单击“连接”。 此时会在 SSMS 中打开“对象资源管理器”窗口。 

5. 在对象资源管理器中展开“数据库”，然后展开 **mySampleDatabase**，查看示例数据库中的对象。

   ![数据库对象](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database-with-ssms"></a>使用 SSMS 在数据库中创建表 

使用 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) 创建具有 4 个表格的数据库架构，这些表格是大专院校的学生管理系统的模型：

- 人员
- 课程
- 学生
- 学分，可作为大专院校的学生管理系统的模型

以下关系图显示了这些表的相互关系。 其中一些表引用其他表中的列。 例如，学生表引用**人员**表的 **PersonId** 列。 请研究此关系图，了解本教程中各种表的相互关系。 若要深入了解如何创建有效的数据库表，请参阅[创建有效的数据库表](https://msdn.microsoft.com/library/cc505842.aspx)。 有关选择数据类型的信息，请参阅[数据类型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql)。

> [!NOTE]
> 还可以使用 [SQL Server Management Studio 中的表设计器](https://msdn.microsoft.com/library/hh272695.aspx)来创建和设计表。 

![表关系](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. 在“对象资源管理器”中，右键单击“mySampleDatabase”，然后单击“新建查询”。 此时会打开一个空白查询窗口，该窗口连接到数据库。

2. 在查询窗口中，执行以下查询以在数据库中创建 4 个表： 

   ```sql 
   -- Create Person table

   CREATE TABLE Person
   (
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![创建表](./media/sql-database-design-first-database/create-tables.png)

3. 展开 SQL Server Management Studio 对象资源管理器中的“表”节点以查看你创建的表。

   ![创建的 ssms 表](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables-with-ssms"></a>使用 SSMS 将数据加载到表

1. 在“下载”文件夹中创建名为 **SampleTableData** 的文件夹，为数据库存储示例数据。 

2. 右键单击以下链接并将它们保存到 **SampleTableData** 文件夹。 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. 打开命令提示符窗口并导航到 SampleTableData 文件夹。

4. 执行以下命令，将示例数据插入表，使用环境值替换 **ServerName**、**DatabaseName**、**UserName** 和 **Password** 的值。
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

现已将示例数据加载到了之前创建的表中。

## <a name="query-the-tables-with-ssms"></a>使用 SSMS 查询表

执行以下查询，从数据库表中检索信息。 有关写入 SQL 查询的详细信息，请参阅[写入 SQL 查询](https://technet.microsoft.com/library/bb264565.aspx)。 第一个查询将联接所有 4 个表，以查找由“Dominick Pope”授课的班级中分数高于 75% 的所有学生。 第二个查询将联接所有 4 个表，以查找“Noe Coleman”注册过的所有课程。

1. 在 SQL Server Management Studio 查询窗口中，执行以下查询：

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

   SELECT  person.FirstName,
   person.LastName,
   course.Name,
   credit.Grade
   FROM  Person AS person
   INNER JOIN Student AS student ON person.PersonId = student.PersonId
   INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
   INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope' 
   AND Grade > 75
   ```

2. 在 SQL Server Management Studio 查询窗口中，执行以下查询：

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

   SELECT  course.Name,
   course.Teacher,
   credit.Grade
   FROM  Course AS course
   INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
   INNER JOIN Student AS student ON student.StudentId = credit.StudentId
   INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
   AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-portal"></a>使用 Azure 门户将数据库还原到之前的时间点

假设你意外删除了某个表。 这是你不能轻易还原的内容。 借助 Azure SQL 数据库，可返回到最近 35 天内的任意时间点并将此时间点还原到新的数据库。 可以通过此数据库恢复已删除的数据。 以下步骤将示例数据库还原到添加这些表之前的时间点。

1. 在数据库的“SQL 数据库”页上，单击工具栏上的“还原”。 将打开“还原”页面。

   ![还原](./media/sql-database-design-first-database/restore.png)

2. 使用必需信息填充“还原”窗体：
    * 数据库名称：提供数据库名称 
    * 时间点：选择“还原”窗体上的“时间点”选项卡 
    * 还原点：选择更改数据库前的时间
    * 目标服务器：还原数据库时不能更改此值 
    * 弹性数据库池：选择“无”  
    * 定价层：选择“20 个 DTU”和“250 GB”的存储。

   ![还原点](./media/sql-database-design-first-database/restore-point.png)

3. 单击“确定”，将数据库[还原到添加这些表之前的时间点](sql-database-recovery-using-backups.md#point-in-time-restore)。 只要在[服务层](sql-database-service-tiers.md)的保留期限内，将数据库还原到不同的时间点就会在与指定时间点的原始数据库相同的服务器中创建一个重复的数据库。

## <a name="next-steps"></a>后续步骤 
本教程介绍了基本数据库任务，例如创建数据库和表、负载和查询数据，以及将数据库还原到以前的时间点。 你已了解如何：
> [!div class="checklist"]
> * 创建数据库
> * 设置防火墙规则。
> * 使用 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) 连接到该数据库
> * 创建表
> * 批量加载数据
> * 查询该数据
> * 使用 SQL 数据库的[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)功能将数据库还原到上一个时间点。请继续学习下一篇教程了解如何迁移数据。

> [!div class="nextstepaction"]
>[将 SQL Server 数据库迁移至 Azure SQL 数据库](sql-database-migrate-your-sql-server-database.md)

