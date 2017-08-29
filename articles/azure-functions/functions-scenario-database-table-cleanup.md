---
title: "使用 Azure Functions 执行数据库清理任务 | Microsoft Docs"
description: "使用 Azure Functions 计划连接到 Azure SQL 数据库的任务，以定期清理行。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 6fd0e32374827b249f5aba1cbfc39117c88c6272
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017

---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>使用 Azure Functions 连接 Azure SQL 数据库
本主题介绍了如何使用 Azure Functions 创建计划作业，以清理 Azure SQL 数据库中的表的行。 根据 Azure 门户中预定义的计时器触发器模板新建 C# 函数。 若要支持此方案，还必须在函数应用中设置数据库连接字符串。 该方案使用针对数据库的大容量操作。 若要让函数处理移动应用表中的单个 CRUD 操作，应改为使用[移动应用绑定](functions-bindings-mobile-apps.md)。

## <a name="prerequisites"></a>先决条件

+ 本主题中将使用计时器触发的函数。 按[在 Azure 中创建由计时器触发的函数](functions-create-scheduled-function.md)主题中的步骤进行操作，创建此函数的 C# 版本。   

+ 本主题演示的是 Transact-SQL 命令，该命令在 AdventureWorksLT 示例数据库的 SalesOrderHeader 表中执行批量清理操作。 若要创建 AdventureWorksLT 示例数据库，请按[在 Azure 门户中创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)主题中的步骤进行操作。 

## <a name="get-connection-information"></a>获取连接信息

完成[在 Azure 门户中创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)中的步骤后，需获取所建数据库的连接字符串。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
 
3. 选择左侧菜单中的“SQL 数据库”，然后在“SQL 数据库”页面上选择数据库。

4. 选择“显示数据库连接字符串”，并复制完整的 ADO.NET 连接字符串。

    ![复制 ADO.NET 连接字符串。](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>设置连接字符串 

Function App 在 Azure 中托管函数的执行。 它是在 Function App 设置中存储连接字符串和其他机密信息的最佳做法。 使用应用程序设置可防止在代码中意外泄漏连接字符串。 

1. 导航到按照[在 Azure 中创建由计时器触发的函数](functions-create-scheduled-function.md)中所述步骤创建的函数应用。

2. 选择“平台功能” > “应用程序设置”。
   
    ![函数应用的应用程序设置。](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. 向下滚动到“连接字符串”，并按表中指定通过设置添加连接字符串。
   
    ![将连接字符串添加到函数应用设置。](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | 设置       | 建议的值 | 说明             | 
    | ------------ | ------------------ | --------------------- | 
    | **Name**  |  sqldb_connection  | 用于访问函数代码中存储的连接字符串。    |
    | **值** | 复制的字符串  | 使用在上一部分中复制的连接字符串。 |
    | **类型** | SQL 数据库 | 使用默认的 SQL 数据库连接。 |   

3. 单击“保存” 。

现在，可以添加连接到 SQL 数据库的 C# 函数代码。

## <a name="update-your-function-code"></a>更新函数代码

1. 在函数应用中，选择由计时器触发的函数。
 
3. 在现有函数代码的顶部添加以下程序集引用：

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```

3. 向函数添加以下 `using` 语句：
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. 将现有 **Run** 函数替换为以下代码：
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    此示例命令会根据装运日期更新“状态”列。 应更新 32 行数据。

5. 单击“保存”，查看下一次执行函数时的“日志”窗口，请注意 SalesOrderHeader 表中更新的行数。

    ![查看函数日志。](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>后续步骤

接下来了解如何将 Functions 与逻辑应用配合使用，以集成其他服务。

> [!div class="nextstepaction"] 
> [创建与逻辑应用集成的函数](functions-twitter-email.md)

有关 Functions 的详细信息，请参阅以下主题：

* [Azure Functions 开发人员参考](functions-reference.md)  
  ，用于编码函数和定义触发器及绑定的程序员参考。
* [测试 Azure Functions](functions-test-a-function.md)  
  介绍可用于测试函数的各种工具和技巧。  

