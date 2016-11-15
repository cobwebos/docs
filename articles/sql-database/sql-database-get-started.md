---
title: "SQL 数据库教程：创建 SQL 数据库 | Microsoft Docs"
description: "了解如何设置 SQL 数据库逻辑服务器、服务器防火墙规则、SQL 数据库和示例数据。 此外，了解如何连接客户端工具、配置用户以及设置数据库防火墙规则。"
keywords: "SQL 数据库教程：创建 SQL 数据库"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 914d5f7e6b8e9165fc8ba9021e1030b865ba5fb2


---
# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>SQL 数据库教程：使用 Azure 门户在几分钟内创建一个 SQL 数据库
> [!div class="op_single_selector"]
> * [Azure 门户](sql-database-get-started.md)
> * [C#](sql-database-get-started-csharp.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> 
> 

本教程介绍如何使用 Azure 门户来完成以下操作：

* 使用示例数据创建 Azure SQL 数据库。
* 创建单个 IP 地址或一系列 IP 地址的服务器级别防火墙规则。

也可以使用 [C#](sql-database-get-started-csharp.md) 或 [PowerShell](sql-database-get-started-powershell.md) 来执行上述相同的任务。

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>创建你的第一个 Azure SQL 数据库
1. 如果当前未连接到 [Azure 门户](http://portal.azure.com)，请连接。
2. 依次单击“新建”和“数据 + 存储”，然后找到“SQL 数据库”。
   
    ![新 SQL 数据库 1](./media/sql-database-get-started/sql-database-new-database-1.png)
3. 单击“SQL 数据库”  ，打开“SQL 数据库”边栏选项卡。 此边栏选项卡上的内容可能不同，具体取决于订阅和现有对象（如现有服务器）的数目。
   
    ![新 SQL 数据库 2](./media/sql-database-get-started/sql-database-new-database-2.png)
4. 在“数据库名称”  文本框中，为第一个数据库提供名称，如“my-database”。 绿色的对勾表示已提供有效名称。
   
    ![新 SQL 数据库 3](./media/sql-database-get-started/sql-database-new-database-3.png)
5. 如果有多个订阅，请选择一个订阅。
6. 在“资源组”下面，单击“新建”并提供第一个资源组的名称 - 例如“my-resource-group”。 绿色的对勾表示已提供有效名称。
   
    ![新 SQL 数据库 4](./media/sql-database-get-started/sql-database-new-database-4.png)
7. 在“选择源”下面单击“示例”，然后在“选择示例”下面单击“AdventureWorksLT [V12]”。
   
    ![新 SQL 数据库 5](./media/sql-database-get-started/sql-database-new-database-5.png)
8. 在“服务器”下面，单击“配置所需的设置”。
   
    ![新 SQL 数据库 6](./media/sql-database-get-started/sql-database-new-database-6.png)
9. 在“服务器”边栏选项卡中，单击“创建新服务器” 。 可在服务器对象中创建 Azure SQL 数据库，Azure SQL 数据库可以是新的服务器，也可以是现有服务器。
   
    ![新 SQL 数据库 7](./media/sql-database-get-started/sql-database-new-database-7.png)
10. 查看“新服务器”  边栏选项卡，了解需要为新服务器提供的信息。
    
    ![新 SQL 数据库 8](./media/sql-database-get-started/sql-database-new-database-8.png)
11. 在“服务器名称”  文本框中，为第一台服务器提供名称，如“my-new-server-object”。 绿色的对勾表示已提供有效名称。
    
    ![新 SQL 数据库 9](./media/sql-database-get-started/sql-database-new-database-9.png)
12. 在“服务器管理员登录名” 下，为此服务器的管理员登录名提供用户名，如“my-admin-account”。 此登录名称为服务器主体登录名。 绿色的对勾表示已提供有效名称。
    
    ![新 SQL 数据库 10](./media/sql-database-get-started/sql-database-new-database-10.png)
13. 在“密码”和“确认密码”下面，提供服务器主体登录帐户的密码 - 例如"p@ssw0rd1".绿色的对勾表示已提供有效密码。
    
    ![新 SQL 数据库 11](./media/sql-database-get-started/sql-database-new-database-11.png)
14. 在“位置” 下，选择适合所在位置的数据中心，如“澳大利亚东部”。
    
    ![新 SQL 数据库 12](./media/sql-database-get-started/sql-database-new-database-12.png)
15. 在“创建 V12 服务器(最新更新)”下，注意只能创建最新版本的 Azure SQL 服务器。
    
    ![新 SQL 数据库 13](./media/sql-database-get-started/sql-database-new-database-13.png)
16. 注意，“允许 Azure 服务访问服务器”  的复选框默认处于选中状态，不能在此处更改。 这是一个高级选项。 可以在此服务器对象的服务器防火墙设置中更改此设置，但大多数情况下没有必要。
    
    ![新 SQL 数据库 14](./media/sql-database-get-started/sql-database-new-database-14.png)
17. 在“新服务器”边栏选项卡中，查看所选内容，单击“选择”  ，为新数据库选择此新服务器。
    
    ![新 SQL 数据库 15](./media/sql-database-get-started/sql-database-new-database-15.png)
18. 在“SQL 数据库”边栏选项卡中的“定价层”下面，单击“S2 标准”，然后单击“基本”，为第一个数据库选择价格最低的定价层。 之后可以随时更改定价层。
    
    ![新 SQL 数据库 16](./media/sql-database-get-started/sql-database-new-database-16.png)
19. 在“SQL 数据库”边栏选项卡中，查看所选内容，单击“创建”  ，创建第一个服务器和数据库。 验证提供的值，然后开始部署。
    
    ![新 SQL 数据库 17](./media/sql-database-get-started/sql-database-new-database-17.png)
20. 在门户工具栏中，单击“通知”  项目，查看部署的状态。
    
    ![新 SQL 数据库 18](./media/sql-database-get-started/sql-database-new-database-18.png)

> [!IMPORTANT]
> 部署完成后，就在 Azure 中创建好新的 Azure SQL 服务器和数据库。 使用 SQL Server 工具无法连接到新服务器或数据库，除非创建服务器防火墙规则，对 Azure 外部的连接开放 SQL 数据库防火墙。
> 
> 

[!INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>后续步骤
完成本 SQL 数据库教程并使用一些示例数据创建好一个数据库后，即可使用偏好的工具来探索其他功能。

* 如果熟悉 Transact-SQL 和 SQL Server Management Studio (SSMS)，请学习如何 [使用 SSMS 连接和查询 SQL 数据库](sql-database-connect-query-ssms.md)。
* 如果了解 Excel，请学习如何 [使用 Excel 连接到 Azure 中的 SQL 数据库](sql-database-connect-excel.md)。
* 如果已准备好开始编码，请在 [用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)中选择所需的编程语言。
* 如果希望将本地 SQL Server 数据库移到 Azure，请参阅 [将数据库迁移到 SQL 数据库](sql-database-cloud-migrate.md) 了解详细信息。
* 如果希望使用 BCP 命令行工具将 CSV 文件中的某些数据加载到新的表中，请参阅 [使用 BCP 将 CSV 文件中的数据加载到 SQL 数据库](sql-database-load-from-csv-with-bcp.md)。
* 如果希望开始探索 Azure SQL 数据库安全性，请参阅 [安全性入门](sql-database-get-started-security.md)

## <a name="additional-resources"></a>其他资源
[什么是 SQL 数据库？](sql-database-technical-overview.md)




<!--HONumber=Nov16_HO2-->


