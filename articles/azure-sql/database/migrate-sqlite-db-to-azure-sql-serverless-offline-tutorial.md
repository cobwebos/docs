---
title: 教程：如何将 SQLite 数据库迁移到 Azure SQL 数据库无服务器
description: 了解如何使用 Azure 数据工厂执行从 SQLite 到 Azure SQL 数据库无服务器的脱机迁移。
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: 6e60403344a0341b4aee74a001287c09ba67e114
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448895"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>如何将 SQLite 数据库迁移到 Azure SQL 数据库无服务器
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

许多人在 SQLite 中首次体验了数据库和 SQL 编程。 SQLite 包含在许多操作系统和热门应用程序中，是世界上最广泛部署和使用的数据库引擎之一。 此外，它可能是许多人首次使用的数据库引擎，因此往往成为项目或应用程序的核心组成部分。 当项目或应用程序超过最初的 SQLite 实施规模时，开发人员可能需要将其数据迁移到可靠的集中式数据存储。

Azure SQL 数据库无服务器计算层是适用于单一数据库的计算层，可根据工作负载需求自动缩放，并根据每秒使用的计算资源量计费。 此外，当仅对存储计费时，无服务器计算层将在非活动期间自动暂停数据库；当活动返回时，它将自动恢复数据库。

完成以下步骤后，数据库将会迁移到 Azure SQL 数据库无服务器，使你能够将数据库提供给云中的其他用户或应用程序使用，而且只需为使用的资源付费，只需对应用程序代码进行极少量的更改。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 订阅
- 要迁移的 SQLite2 或 SQLite3 数据库
- 一个 Windows 环境
  - 如果没有本地 Windows 环境，可以使用 Azure 中的 Windows VM 进行迁移。 使用 Azure 文件存储和存储资源管理器在 VM 上移动 SQLite 数据库文件并使其可用。

## <a name="steps"></a>步骤

1. 在“无服务器”计算层中预配新的 Azure SQL 数据库。

    ![Azure 门户的屏幕截图，其中显示了 Azure SQL 数据库无服务器的预配示例](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. 确保 SQLite 数据库文件在 Windows 环境中可用。 如果没有 SQLite ODBC 驱动程序，请安装该驱动程序 (有许多可用的开源驱动程序，例如 http://www.ch-werner.de/sqliteodbc/) 。

3. 为数据库创建系统 DSN。 确保使用与系统体系结构匹配的数据源管理器应用程序（32 位或 64 位）。 可以在系统设置中查找正在运行的版本。

    - 在环境中打开 ODBC 数据源管理器。
    - 依次单击“系统 DSN”选项卡、“添加”
    - 选择已安装的 SQLite ODBC 连接器，并为连接指定一个有意义的名称，例如 sqlitemigrationsource
    - 在 .db 文件中设置数据库名称
    - 保存并退出

4. 下载并安装自承载集成运行时。 为此，最简单的方法是根据文档中的详述使用“快速安装”选项。 如果选择手动安装，则需要为应用程序提供身份验证密钥，可在数据工厂实例中通过以下操作找到该密钥：

    - 启动 ADF（在 Azure 门户上的服务中选择“创作和监视”）
    - 单击左侧的“创作”选项卡（蓝色铅笔图标）
    - 单击“连接”（左下角）并选择“集成运行时”
    - 添加新的自承载集成运行时，为其命名，然后选择“选项 2”。

5. 为数据工厂中的源 SQLite 数据库创建新的链接服务。

    ![显示 Azure 数据工厂中的空“链接服务”边栏选项卡的屏幕截图](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. 在“连接”中的“链接服务”下，单击“新建”  。

7. 搜索并选择“ODBC”连接器

   ![显示 Azure 数据工厂中“链接服务”边栏选项卡上的“ODBC”连接器徽标的屏幕截图](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. 为该链接服务指定一个有意义的名称，例如“sqlite_odbc”。 从“通过集成运行时进行连接”下拉列表中选择你的集成运行时。 在连接字符串中输入以下内容（请将 Initial Catalog 变量替换为 .db 文件的文件路径，并将 DSN 替换为系统 DSN 连接的名称）：

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. 将身份验证类型设置为“匿名”

10. 测试连接

    ![显示 Azure 数据工厂中的成功连接的屏幕截图](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. 为无服务器 SQL 目标创建另一个链接服务。 使用链接服务向导选择数据库，并提供 SQL 身份验证凭据。

    ![显示 Azure 数据工厂中选择的 Azure SQL 数据库的屏幕截图](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. 从 SQLite 数据库提取 CREATE TABLE 语句。 为此，可对数据库文件执行以下 Python 脚本。

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

13. 通过从 CreateTables.sql 文件中复制 CREATE TABLE 语句并在 Azure 门户上的查询编辑器中运行 SQL 语句，在无服务器 SQL 目标环境中创建登陆表。

14. 返回到数据工厂的主屏幕，然后单击“复制数据”来运行作业创建向导中的每个步骤。

    ![显示 Azure 数据工厂中的“复制数据向导”徽标的屏幕截图](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. 使用复选框选择源 SQLite 数据库中的所有表，并将其映射到 Azure SQL 中的目标表。 作业完成运行后，即已成功将数据从 SQLite 迁移到 Azure SQL！

## <a name="next-steps"></a>后续步骤

- 若要入门，请参阅[快速入门：使用 Azure 门户在 Azure SQL 数据库中创建单一数据库](single-database-create-quickstart.md)。
- 有关资源限制的信息，请参阅[无服务器计算层资源限制](../../sql-database/sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。
