---
title: 使用具有 .NET 和 C# 的 Visual Studio 进行查询
description: 使用 Visual Studio 创建与 Azure SQL 数据库中的数据库或 Azure SQL 托管实例连接并运行查询的 C# 应用。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: ed0ad6c5597d63c411eeb323cf00dcb7f044f667
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067811"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入门：使用 Visual Studio 中的 .NET 和 C# 来连接和查询 Azure SQL 数据库中的数据库或 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本快速入门展示了如何使用 Visual Studio 中的 [.NET Framework](https://www.microsoft.com/net/) 和 C# 代码通过 Transact-SQL 语句查询 Azure SQL 数据库中的数据库。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure SQL 数据库中的数据库。 可以根据下述快速入门之一，在 Azure SQL 数据库中创建数据库，然后对其进行配置：

  | 操作 | SQL 数据库 | SQL 托管实例 | Azure VM 上的 SQL Server |
  |:--- |:--- |:---|:---|
  | 创建| [门户](single-database-create-quickstart.md) | [门户](../managed-instance/instance-create-quickstart.md) | [门户](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 配置 | [服务器级别 IP 防火墙规则](firewall-create-server-level-portal-quickstart.md)| [从 VM 进行连接](../managed-instance/connect-vm-instance-configure.md)|
  |||[从本地建立的连接](../managed-instance/point-to-site-p2s-configure.md) | [连接到 SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |加载数据|根据快速入门加载的 Adventure Works|[还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 的一个 [BACPAC](database-import.md) 文件还原或导入 Adventure Works| 从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 所提供的 [BACPAC](database-import.md) 文件还原或导入 Adventure Works|
  |||

  > [!IMPORTANT]
  > 本文中脚本的编写目的是使用 Adventure Works 数据库。 使用 SQL 托管实例时，必须将 Adventure Works 数据库导入一个实例数据库，或者修改本文中的脚本，以便使用 Wide World Importers 数据库。

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community、Professional 或 Enterprise 版本。

## <a name="get-server-connection-information"></a>获取服务器连接信息

获取连接到数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 导航到“SQL 数据库”或“SQL 托管实例”页。

3. 在“概述”页上，在“Server 名称”旁查看 Azure SQL 数据库中的数据库的完全限定服务器名称，或在“Host”旁边查看 Azure VM 上的 Azure SQL 托管实例或 SQL Server 的完全限定服务器名称（或 IP 地址）  。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标。

> [!NOTE]
> 有关 Azure VM 上的 SQL Server 的连接信息，请参阅[连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)。

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>创建用于查询 Azure SQL 数据库中的数据库的代码

1. 在 Visual Studio 中，创建新的项目。 
   
1. 在“新建项目”对话框中，选择“Visual C#”，然后选择“控制台应用(.NET Framework)”  。
   
1. 输入“sqltest”作为项目名称，然后选择“确定”。 创建新项目。 
   
1. 选择“项目” > “管理 NuGet 包” 。 
   
1. 在“NuGet 包管理器”中，选择“浏览”选项卡，然后搜索并选择“Microsoft.Data.SqlClient”  。
   
1. 在“Microsoft.Data.SqlClient”页上选择“安装” 。 
   - 如果出现提示，请选择“确定”继续安装。 
   - 如果显示“接受许可证”窗口，则选择“我接受” 。
   
1. 安装完成后，可以关闭“NuGet 包管理器”。 
   
1. 在代码编辑器中，将 Program.cs 内容替换为以下代码。 替换 `<server>`、`<username>`、`<password>` 和 `<database>` 的值。
   
   >[!IMPORTANT]
   >本示例中的代码使用示例 AdventureWorksLT 数据，在创建数据库时可以选择该数据作为源。 如果数据库有不同数据，请在 SELECT 查询中使用自己数据库中的表。 
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>运行代码

1. 若要运行该应用，请选择“调试” > “开始调试”，或选择工具栏上的“开始”，或按 F5   。
1. 验证是否返回了数据库中的前 20 个类别/产品行，然后关闭应用窗口。

## <a name="next-steps"></a>后续步骤

- 了解如何在 Windows/Linux/macOS 中[使用 .NET Core 连接和查询 Azure SQL 数据库中的数据库](connect-query-dotnet-core.md)。  
- 了解[在 Windows/Linux/macOS 中通过命令行使用 .NET Core 入门](/dotnet/core/tutorials/using-with-xplat-cli)。
- 了解如何[使用 SSMS 在 Azure SQL 数据库中设计你的第一个数据库](design-first-database-tutorial.md)，或者如何[使用 .NET 在 Azure SQL 数据库中设计你的第一个数据库](design-first-database-csharp-tutorial.md)。
- 有关 .NET 的详细信息，请参阅 [.NET 文档](https://docs.microsoft.com/dotnet/)。
- 重试逻辑示例：[使用 ADO.NET 弹性连接到 Azure SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]。


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

