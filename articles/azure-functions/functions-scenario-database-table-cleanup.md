---
title: 使用 Azure Functions 执行数据库清理任务
description: 使用 Azure Functions 计划连接到 Azure SQL 数据库的任务，以定期清理行。
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 2e3f53943d45e90b8aff8e386ce8d0e28670673f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366797"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>使用 Azure Functions 连接 Azure SQL 数据库

本文介绍如何使用 Azure Functions 创建连接到 Azure SQL 数据库或 Azure SQL 托管实例的计划作业。 该函数代码用于清除数据库表中的行。 新C#函数是基于 Visual Studio 2019 中预定义的计时器触发器模板创建的。 若要支持这种情况，还必须设置数据库连接字符串，使其成为函数应用中的应用设置。 对于 Azure SQL 托管实例需要[启用公共终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)才能从 Azure Functions 进行连接。 该方案使用针对数据库的大容量操作。 

如果首次使用 C# 函数，则应阅读 [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)。

## <a name="prerequisites"></a>必备条件

+ 完成[使用 Visual Studio 创建第一个函数一](functions-create-your-first-function-visual-studio.md)文中的步骤，创建面向版本2.x 或更高版本的运行时的本地函数应用。 此外，还必须已将项目发布到 Azure 中的函数应用。

+ 本文演示的是 Transact-SQL 命令，该命令在 AdventureWorksLT 示例数据库的 SalesOrderHeader 表中执行批量清理操作。 若要创建 AdventureWorksLT 示例数据库，请按[在 Azure 门户中创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)一文中的步骤进行操作。

+ 必须针对用于本快速入门的计算机的公共 IP 地址添加[服务器级防火墙规则](../sql-database/sql-database-get-started-portal-firewall.md)。 必须提供此规则才能从本地计算机访问 SQL 数据库实例。  

## <a name="get-connection-information"></a>获取连接信息

完成[在 Azure 门户中创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)中的步骤后，需获取所建数据库的连接字符串。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 选择左侧菜单中的“SQL 数据库”，然后在“SQL 数据库”页面上选择数据库。

1. 选择“设置”下的“连接字符串”，并复制完整的 ADO.NET 连接字符串。 对于 Azure SQL 托管实例为公用终结点复制连接字符串。

    ![复制 ADO.NET 连接字符串。](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>设置连接字符串

Function App 在 Azure 中托管函数的执行。 这是在函数应用设置中存储连接字符串和其他机密信息的最佳安全做法。 使用应用程序设置可防止在代码中意外泄漏连接字符串。 可以直接从 Visual Studio 访问函数应用的应用设置。

但前提是必须已将应用发布到 Azure。 若尚未执行此操作，[请将函数应用发布到 Azure ](functions-develop-vs.md#publish-to-azure)。

1. 在解决方案资源管理器中，右键单击函数应用项目，然后选择 "**发布** > **编辑 Azure App Service 设置**"。 选择“添加设置”，在“新建应用设置名称”中键入 **然后选择“确认”** `sqldb_connection`。

    ![函数应用的应用程序设置。](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. 在新的“sqldb_connection”设置中，将上一部分复制的连接字符串粘贴到“本地”，并将 **和** 占位符替换为实际值`{your_username}``{your_password}`。 选择“从本地插入值”以将更新值复制到“远程”字段，然后选择“确认”。

    ![添加 SQL 连接字符串设置。](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    连接字符串以加密方式存储在 Azure 中（远程）。 为防止泄密，应从源代码管理中排除 local.settings.json 项目文件（本地），例如，通过使用 .gitignore 文件完成。

## <a name="add-the-sqlclient-package-to-the-project"></a>将 SqlClient 包添加到项目中

你需要添加包含 SqlClient 库的 NuGet 包。 需要此数据访问库以连接到 SQL 数据库。

1. 在 Visual Studio 2019 中打开本地 function app 项目。

1. 在“解决方案资源管理器”中，右键单击函数应用项目，并选择“管理 NuGet 包”。

1. 转到“浏览”选项卡，搜索 ```System.Data.SqlClient```，找到后将其选中。

1. 在“System.Data.SqlClient”页面，选择版本 **，然后单击“安装”** `4.5.1`。

1. 安装完成后，查看所做的更改，然后单击“确定”以关闭“预览”窗口。

1. 如果显示“接受许可证”窗口，则单击“我接受”。

现在，可以添加连接到 SQL 数据库的 C# 函数代码。

## <a name="add-a-timer-triggered-function"></a>添加计时器触发的函数

1. 在“解决方案资源管理器”中，右键单击函数应用项目，并选择“添加” **“新 Azure 函数”**  > 。

1. 选择“Azure Functions”模板后，请按照类似  **的格式命名新项目并选择“添加”** `DatabaseCleanup.cs`。

1. 在“新 Azure 函数”对话框中选择“计时器触发器”，然后选择“确认”。 此对话框为定时器触发器函数创建代码文件。

1. 打开新代码文件，然后在文件的顶部添加以下 using 语句：

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. 将现有 `Run` 函数替换为以下代码：

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    此函数每 15 秒运行一次，以根据发货日期更新 `Status` 列。 要了解有关计时器触发器的详细信息，请参阅 [Azure Functions 的定时器触发器](functions-bindings-timer.md)。

1. 按“F5”启动函数应用。 [Azure Functions Core Tools](functions-develop-local.md) 执行窗口在 Visual Studio 后台打开。

1. 启动后 15 秒，该函数将运行。 观察输出并记下 SalesOrderHeader 表中更新的行数。

    ![查看函数日志。](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    在第一次执行时，应更新 32 行数据。 以下运行不更新任何数据行，除非对 SalesOrderHeader 表数据进行更改，以便 `UPDATE` 语句选择更多行。

如果打算[发布此函数](functions-develop-vs.md#publish-to-azure)，请记得将 `TimerTrigger` 属性更改为比每 15 秒计划更合理的[cron 计划](functions-bindings-timer.md#ncrontab-expressions)。

## <a name="next-steps"></a>后续步骤

下一步，了解如何使用。 将 Functions 与逻辑应用配合使用，以集成其他服务。

> [!div class="nextstepaction"]
> [创建与逻辑应用集成的函数](functions-twitter-email.md)

若要深入了解 Functions，请参阅以下文章：

+ [Azure Functions 开发人员参考](functions-reference.md)  
  ，用于编码函数和定义触发器及绑定的程序员参考。
+ [测试 Azure Functions](functions-test-a-function.md)  
  介绍可用于测试函数的各种工具和技巧。  
