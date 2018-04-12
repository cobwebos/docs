---
title: 使用 .NET Core 查询 Azure SQL 数据库 | Microsoft Docs
description: 本主题介绍如何使用 .NET Core 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 3121de4fdcd9814bae2956e6bdfa580d54ffca92
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="use-net-core-c-to-query-an-azure-sql-database"></a>使用 .NET Core (C#) 查询 Azure SQL 数据库

本快速入门教程演示了如何在 Windows/Linux/macOS 中使用 [.NET Core](https://www.microsoft.com/net/) 来创建连接到 Azure SQL 数据库的 C# 程序，并使用 Transact-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门教程，请确保符合以下条件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 针对用于本快速入门教程的计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- 你已安装[适用于操作系统的 .NET Core](https://www.microsoft.com/net/core)。 

## <a name="sql-server-connection-information"></a>SQL Server 连接信息

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="for-adonet"></a>适用于 ADO.NET

1. 单击“显示数据库连接字符串”继续。

2. 查看完整的 ADO.NET 连接字符串。

    ![ADO.NET 连接字符串](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> 对于在其上执行本教程操作的计算机，必须为其公共 IP 地址制定防火墙规则。 如果使用其他计算机或其他公共 IP 地址，则[使用 Azure 门户创建服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。 
>
  
## <a name="create-a-new-net-project"></a>创建新的 .NET 项目

1. 打开命令提示符，然后创建一个名为 sqltest 的文件夹。 导航到已创建的文件夹，并运行以下命令：

    ```
    dotnet new console
    ```

2. 使用喜欢的文本编辑器打开 sqltest.csproj，然后使用以下代码以依赖项方式添加 System.Data.SqlClient：

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.4.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>插入用于查询 SQL 数据库的代码

1. 在开发环境或喜欢的文本编辑器中，打开 Program.cs

2. 将内容替换为以下代码，为服务器、数据库、用户和密码添加相应的值。

```csharp
using System;
using System.Data.SqlClient;
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
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
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

1. 请在命令提示符处运行以下命令：

   ```csharp
   dotnet restore
   dotnet run
   ```

2. 验证是否已返回前 20 行，然后关闭应用程序窗口。


## <a name="next-steps"></a>后续步骤

- [在 Windows/Linux/macOS 中通过命令行使用 .NET Core 入门](/dotnet/core/tutorials/using-with-xplat-cli)。
- 了解如何[使用 .NET Framework 和 Visual Studio 连接和查询 Azure SQL 数据库](sql-database-connect-query-dotnet-visual-studio.md)。  
- 了解如何[使用 SSMS 设计你的第一个 Azure SQL 数据库](sql-database-design-first-database.md)，或者如何[使用 .NET 设计你的第一个 Azure SQL 数据库](sql-database-design-first-database-csharp.md)。
- 有关 .NET 的详细信息，请参阅 [.NET 文档](https://docs.microsoft.com/dotnet/)。
