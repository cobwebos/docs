---
title: 使用 .NET Core 查询 Azure SQL 数据库 | Microsoft Docs
description: 本主题介绍如何使用 .NET Core 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/21/2018
ms.openlocfilehash: 0b6e5116a90c66852ac39f67f9f32c94470e5332
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565320"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>快速入门：使用 .NET Core (C#) 查询 Azure SQL 数据库

在本快速入门中，将使用 [.NET Core](https://www.microsoft.com/net/) 和 C# 代码连接到 Azure SQL 数据库。 然后，将运行 Transact-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

对于本教程的内容，你需要：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 已安装[适用于操作系统的 .NET Core](https://www.microsoft.com/net/core)。 

> [!NOTE]
> 本快速入门使用 mySampleDatabase 数据库。 若要使用其他数据库，需更改数据库引用并修改 C# 代码中的 `SELECT` 查询。


## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="get-adonet-connection-information-optional"></a>获取 ADO.NET 连接信息（可选）

1. 导航到“mySampleDatabase”页，并在“设置”下选择“连接字符串”。

2. 查看完整的 ADO.NET 连接字符串。

    ![ADO.NET 连接字符串](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. 如果想要使用 ADO.NET 连接字符串，请复制它。
  
## <a name="create-a-new-net-core-project"></a>创建新的 .NET Core 项目

1. 打开命令提示符，然后创建一个名为 sqltest 的文件夹。 导航到此文件夹，并运行以下命令。

    ```cmd
    dotnet new console
    ```
    此命令将创建新的应用项目文件，包括初始 C# 代码文件 (**Program.cs**)、XML 配置文件 (**sqltest.csproj**) 和所需的二进制文件。

2. 在文本编辑器中，打开 sqltest.csproj 并在 `<Project>` 标记之间粘贴以下 XML。 此 XML 会添加 `System.Data.SqlClient` 作为依赖项。

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>插入用于查询 SQL 数据库的代码

1. 在文本编辑器中打开 Program.cs 文件。

2. 将内容替换为以下代码，为服务器、数据库、用户名和密码添加相应的值。

> [!NOTE]
> 若要使用 ADO.NET 连接字符串，请将代码中设置服务器、数据库、用户名和密码的 4 行替换为以下行。 在字符串中，设置用户名和密码。
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>运行代码

1. 在提示符处，运行以下命令。

   ```cmd
   dotnet restore
   dotnet run
   ```

2. 验证是否返回前 20 行。

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. 选择 Enter 关闭应用程序窗口。

## <a name="next-steps"></a>后续步骤

- [在 Windows/Linux/macOS 中通过命令行使用 .NET Core 入门](/dotnet/core/tutorials/using-with-xplat-cli)。
- 了解如何[使用 .NET Framework 和 Visual Studio 连接和查询 Azure SQL 数据库](sql-database-connect-query-dotnet-visual-studio.md)。  
- 了解如何[使用 SSMS 设计首个 Azure SQL 数据库](sql-database-design-first-database.md)，或者如何[使用 C# 和 ADO.NET 设计 Azure SQL 数据库并进行连接](sql-database-design-first-database-csharp.md)。
- 有关 .NET 的详细信息，请参阅 [.NET 文档](https://docs.microsoft.com/dotnet/)。
