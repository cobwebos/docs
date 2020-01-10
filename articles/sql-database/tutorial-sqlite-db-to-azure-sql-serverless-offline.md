---
title: 教程：如何将 SQLite 数据库迁移到 Azure SQL 数据库无服务器
description: 了解如何使用 Azure 数据工厂执行从 SQLite 到 Azure SQL 数据库无服务器的脱机迁移。
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780504"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>如何将 SQLite 数据库迁移到 Azure SQL 数据库无服务器
对于许多人来说，SQLite 提供数据库和 SQL 编程的首次体验。 它包含在许多操作系统中，最常用的应用程序会让 SQLite 成为世界上最广泛部署和使用的数据库引擎。 而且，因为它可能是许多人都在使用的第一个数据库引擎，所以通常作为项目或应用程序的一个中心。 在项目或应用程序超过初始 SQLite 实现的这种情况下，开发人员可能需要将其数据迁移到可靠的集中式数据存储。

Azure SQL 数据库无服务器是单一数据库的计算层，可根据工作负荷需求自动缩放计算，并根据每秒使用的计算量来计费。 当只对存储计费并在活动返回时自动恢复数据库时，无服务器计算层还会在非活动期间自动暂停数据库。

完成以下步骤后，你的数据库将迁移到 Azure SQL 数据库无服务器，使你能够将数据库提供给云中的其他用户或应用程序，并且只需为你使用的内容付费，并且只需对应用程序代码进行更改。

## <a name="prerequisites"></a>必备组件
- Azure 订阅
- 要迁移的 SQLite2 或 SQLite3 数据库
- Windows 环境
  - 如果没有本地 Windows 环境，可以使用 Azure 中的 Windows VM 进行迁移。 使用 Azure 文件和存储资源管理器移动并使 SQLite 数据库文件可用于 VM。

## <a name="steps"></a>步骤

1. 在无服务器计算层中预配新的 Azure SQL 数据库。

    ![显示 Azure sql 数据库无服务器的预配示例的 Azure 门户屏幕截图](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. 确保你的 SQLite 数据库文件在 Windows 环境中可用。 如果还没有 SQLite ODBC 驱动程序，请安装该驱动程序（有许多可用的开放源代码，如 http://www.ch-werner.de/sqliteodbc/) 。

3. 为数据库创建系统 DSN。 确保使用与系统体系结构相匹配的数据源管理器应用程序（32位 vs 64）。 您可以在系统设置中找到正在运行的版本。

    - 在环境中打开 ODBC 数据源管理器。
    - 单击 "系统 DSN" 选项卡，然后单击 "添加"
    - 选择已安装的 SQLite ODBC 连接器，并为连接指定一个有意义的名称，例如 sqlitemigrationsource
    - 将数据库名称设置为 .db 文件
    - 保存并退出

4. 下载并安装自承载集成运行时。 要执行此操作，最简单的方法是 "快速安装" 选项，详细信息请参阅文档。 如果选择手动安装，需要为应用程序提供身份验证密钥，该密钥可通过以下方式位于数据工厂实例中：

    - 启动 ADF （从 Azure 门户中的服务创作和监视）
    - 单击左侧的 "作者" 选项卡
    - 单击 "连接" （左下角），然后单击 "集成运行时"
    - 添加新的自承载 Integration Runtime，为其指定一个名称，然后选择 "*选项 2*"。

5. 为数据工厂中的源 SQLite 数据库创建新的链接服务。

    ![在 Azure 数据工厂中显示空链接服务边栏选项卡的屏幕截图](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. 在 "连接" 下的 "链接服务" 下，单击 "新建"

7. 搜索并选择 "ODBC" 连接器


    ![在 Azure 数据工厂的 "链接的服务" 边栏选项卡中显示 ODBC 连接器徽标的屏幕截图](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. 为链接服务指定一个有意义的名称，例如 "sqlite_odbc"。 从 "通过集成运行时连接" 下拉列表中选择集成运行时。 在连接字符串中输入以下内容，将初始目录变量替换为 .db 文件的 filepath，并将 DSN 替换为系统 DSN 连接的名称： 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. 将身份验证类型设置为匿名

10. 测试连接

    ![显示 Azure 数据工厂中的成功连接的屏幕截图](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. 为无服务器 SQL 目标创建另一个链接服务。 使用链接服务向导选择数据库，并提供 SQL 身份验证凭据。

    ![显示在 Azure 数据工厂中选择的 Azure SQL 数据库的屏幕截图](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. 从 SQLite 数据库中提取 CREATE TABLE 语句。 可以通过对数据库文件执行以下 Python 脚本来实现此目的。

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. 通过从 Createtables.sql 文件中复制 CREATE table 语句并在 Azure 门户中的查询编辑器中运行 SQL 语句，在无服务器 SQL 目标环境中创建登陆表。

14. 返回到数据工厂的主屏幕，然后单击 "复制数据" 以通过作业创建向导运行。

    ![显示 Azure 数据工厂中复制数据向导徽标的屏幕截图](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. 使用复选框选择源 SQLite 数据库中的所有表，并将它们映射到 Azure SQL 中的目标表。 作业运行后，已成功将数据从 SQLite 迁移到 Azure SQL！

## <a name="next-steps"></a>后续步骤

- 若要开始操作，请参阅[快速入门：使用 Azure 门户在 AZURE SQL 数据库中创建单个数据库](sql-database-single-database-get-started.md)。
- 有关资源限制的信息，请参阅[无服务器计算层资源限制](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。
