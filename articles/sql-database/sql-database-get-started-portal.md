---
title: "Azure 门户：创建 SQL 数据库 | Microsoft Docs"
description: "了解如何在 Azure 门户中创建 SQL 数据库逻辑服务器、服务器级防火墙规则和数据库。 同时了解如何使用 Azure 门户查询 Azure SQL 数据库。"
keywords: "SQL 数据库教程：创建 SQL 数据库"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 58af25d90b419b3ddb986118a8c9ba3b42aa95a6
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>在 Azure 门户中创建 Azure SQL 数据库

本快速入门教程逐步讲解如何在 Azure 中创建 SQL 数据库。 Azure SQL 数据库是一种“数据库即服务”产品，可用于在云中运行和缩放高可用性 SQL Server 数据库。 本快速入门教程介绍如何开始使用 Azure 门户创建 SQL 数据库。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-sql-database"></a>创建 SQL 数据库

创建 Azure SQL 数据库时，会使用定义好的一组[计算和存储资源](sql-database-service-tiers.md)。 数据库在 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)和 [Azure SQL 数据库逻辑服务器](sql-database-features.md)中创建。 

请按以下步骤创建包含 Adventure Works LT 示例数据的 SQL 数据库。 

1. 单击 Azure 门户左上角的“新建”按钮。

2. 从“新建”页中选择“数据库”，然后从“数据库”页中选择“SQL 数据库”。

    ![创建数据库 - 1](./media/sql-database-get-started-portal/create-database-1.png)

3. 如上图所示，在“SQL 数据库”窗体中填写以下信息：     
   - 数据库名称：**mySampleDatabase**
   - 资源组：**myResourceGroup**
   - 源：**示例(AdventureWorksLT)**

4. 单击“服务器”，为新数据库创建并配置服务器。 填写“新建服务器”窗体，指定全局唯一的服务器名称，提供服务器管理员登录名，然后指定所选的密码。 

    ![创建数据库 - 服务器](./media/sql-database-get-started-portal/create-database-server.png)
5. 单击“选择”。

6. 单击“定价层”为新数据库指定服务层和性能级别。 对于本快速入门，请选择 **20 DTU** 和 **250** GB 存储

    ![创建数据库 - s1](./media/sql-database-get-started-portal/create-database-s1.png)

7. 单击“应用” 。  

8. 单击“创建”预配数据库。 预配需要数分钟。 

9. 在工具栏上，单击“通知”可监视部署过程。

    ![通知](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>创建服务器级防火墙规则

SQL 数据库服务在服务器级别创建一个防火墙。除非创建了防火墙规则来为特定的 IP 地址打开防火墙，否则会阻止外部应用程序和工具连接到服务器或服务器上的任何数据库。 按照以下步骤为客户端 IP 地址创建 [SQL 数据库服务器级防火墙规则](sql-database-firewall-configure.md)，并只允许通过针对你的 IP 地址打开的 SQL 数据库防火墙建立外部连接。 

1. 部署完成后，在左侧菜单中单击“SQL 数据库”，然后在“SQL 数据库”页上单击你的数据库。 此时将打开数据库的概述页，其中显示了完全限定的服务器名称（例如 **mynewserver20170327.database.windows.net**），并提供了其他配置的选项。

      ![服务器防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. 如上图所示，在工具栏上单击“设置服务器防火墙”。 此时会打开 SQL 数据库服务器的“防火墙设置”页。 

3. 在工具栏上单击“添加客户端 IP”，然后单击“保存”。 此时会针对当前的 IP 地址创建服务器级防火墙规则。

      ![设置服务器防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. 单击“确定”，然后关闭“防火墙设置”页。

现在可以使用之前创建的服务器管理员帐户通过 SQL Server Management Studio 或其他所选工具从此 IP 地址连接到 SQL 数据库服务器及其数据库。

> [!NOTE]
> 通过端口 1433 进行的 SQL 数据库通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到 Azure SQL 数据库服务器，除非你的 IT 部门打开了端口 1433。
>

## <a name="query-the-sql-database"></a>查询 SQL 数据库

创建 SQL 数据库时，我们填充了 **AdventureWorksLT** 示例数据库（这是在本本快速入门教程前面的“创建 UI”中选择的选项之一）。 现在，让我们使用 Azure 门户中的内置查询工具来查询数据。 

1. 在数据库的“SQL 数据库”页上，单击工具栏上的“工具”。 此时会打开“工具”页。

     ![工具菜单](./media/sql-database-get-started-portal/tools-menu.png) 

2. 依次单击“查询编辑器(预览版)”、“预览条款”复选框、“确定”。 此时会打开“查询编辑器”页。

3. 单击“登录”，然后在出现提示时选择“SQL Server 身份验证”，然后提供前面创建的服务器管理员登录名和密码。

    ![登录](./media/sql-database-get-started-portal/login.png) 

4. 单击“确定”登录。

5. 完成身份验证后，在查询编辑器窗格中键入以下查询。

   ```
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. 单击“运行”，然后在“结果”窗格中查看查询结果。

    ![查询编辑器结果](./media/sql-database-get-started-portal/query-editor-results.png)

7. 关闭“查询编辑器”页和“工具”页。

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他快速入门教程是在本文的基础上制作的。 如果计划继续使用后续的快速入门，请勿清除在本快速入门中创建的资源。 如果不打算继续，请在 Azure 门户中执行以下步骤，删除通过此快速入门创建的所有资源。

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击“myResourceGroup”。 
2. 在资源组页上单击“删除”，在文本框中键入 **myResourceGroup**，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

- 若要使用 SQL Server Management Studio 进行连接和查询，请参阅[使用 SSMS 进行连接和查询](sql-database-connect-query-ssms.md)
- 若要使用 Visual Studio Code 进行连接和查询，请参阅[使用 Visual Studio Code 进行连接和查询](sql-database-connect-query-vscode.md)。
- 若要使用 .NET 进行连接和查询，请参阅[使用 .NET 进行连接和查询](sql-database-connect-query-dotnet.md)。
- 若要使用 PHP 进行连接和查询，请参阅[使用 PHP 进行连接和查询](sql-database-connect-query-php.md)。
- 若要使用 Node.js 进行连接和查询，请参阅[使用 Node.js 进行连接和查询](sql-database-connect-query-nodejs.md)。
- 若要使用 Java 进行连接和查询，请参阅[使用 Java 进行连接和查询](sql-database-connect-query-java.md)。
- 若要使用 Python 进行连接和查询，请参阅[使用 Python 进行连接和查询](sql-database-connect-query-python.md)。
- 若要使用 Ruby 进行连接和查询，请参阅[使用 Ruby 进行连接和查询](sql-database-connect-query-ruby.md)。

