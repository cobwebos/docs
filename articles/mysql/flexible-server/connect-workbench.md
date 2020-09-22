---
title: 快速入门：连接 - MySQL Workbench - Azure Database for MySQL 灵活服务器
description: 本快速入门分步介绍如何使用 MySQL Workbench 连接到 Azure Database for MySQL 灵活服务器并查询其中的数据。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: d604ca8c8979ec98b990f8002ce29d0df92ac2af
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944068"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>快速入门：使用 MySQL Workbench 连接到 Azure Database for MySQL 灵活服务器（预览版）并查询其中的数据


> [!IMPORTANT] 
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本快速入门演示如何使用 MySQL Workbench 应用程序连接到 Azure Database for MySQL 灵活服务器。

## <a name="prerequisites"></a>先决条件

此快速入门使用以下任意指南中创建的资源作为起点：

- [使用 Azure 门户创建 Azure Database for MySQL 灵活服务器](./quickstart-create-server-portal.md)
- [使用 Azure CLI 创建 Azure Database for MySQL 灵活服务器](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>准备客户端工作站
- 如果使用“专用访问(VNet 集成)”创建了灵活服务器，需要从与服务器相同的 VNet 中的资源连接到服务器。 可以创建虚拟机并将其添加到使用灵活服务器创建的 VNet 中。 请参阅[使用 Azure CLI 创建和管理 Azure Database for MySQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-cli.md)。
- 如果使用“公共访问(允许的 IP 地址)”创建了灵活服务器，可以将本地 IP 地址添加到服务器上的防火墙规则列表中。 请参阅[使用 Azure CLI 创建和管理 Azure Database for MySQL 灵活服务器防火墙规则](./how-to-manage-firewall-cli.md)。

- 在计算机上从 [MySQL 网站](https://dev.mysql.com/downloads/workbench/)下载并安装 MySQL Workbench。

## <a name="get-connection-information"></a>获取连接信息

获取连接到灵活服务器所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户的左侧菜单中，选择“所有资源”，然后搜索已创建的服务器（例如 mydemoserver）。
3. 选择服务器名称。
4. 从服务器的“概览”面板中记下“服务器名称”和“服务器管理员登录名”。   如果忘记了密码，也可通过此面板来重置密码。
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>使用 MySQL Workbench 连接服务器

使用 MySQL Workbench 连接到 Azure Database for MySQL 灵活服务器：

1. 启动计算机上的 MySQL Workbench 应用程序。

2. 在“设置新连接”对话框的“参数”选项卡上，输入以下信息：

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="设置新连接":::

    | **参数** | **建议的值** | **字段说明** |
    |---|---|---|
    |    连接名称 | 演示连接 | 指定此连接的标签。 |
    | 连接方法 | 标准 (TCP/IP) | 标准 (TCP/IP) 就足够了。 |
    | 主机名 | 服务器名称 | 指定此前在创建 Azure Database for MySQL 时使用过的服务器名称值。 显示的示例服务器为 mydemoserver.mysql.database.azure.com。 请使用完全限定的域名 (\*.mysql.database.azure.com)，如示例中所示。 如果记不起服务器名称，请按上一部分的步骤操作，以便获取连接信息。  |
    | 端口 | 3306 | 在连接到 Azure Database for MySQL 时，始终使用端口 3306。 |
    | 用户名 |  服务器管理员登录名 | 键入此前在创建 Azure Database for MySQL 时提供的服务器管理员登录用户名。 示例用户名为 myadmin。 如果记不起用户名，请按上一部分的步骤操作，以便获取连接信息。
    | 密码 | 你的密码 | 单击“在保管库中存储...”按钮来保存密码。 |

3. 单击“测试连接”以测试是否所有参数均已正确配置。

4. 单击“确定”保存连接。

5. 在“MySQL 连接”列表中，单击与服务器对应的磁贴并等待建立连接。

    将打开一个新的 SQL 选项卡，该选项卡包含一个可在其中键入查询的空白编辑器。

> [!NOTE]
> 使用 TLS 1.2 的加密连接是必需的，且在 Azure Database for MySQL 灵活服务器上强制执行。 虽然通常不需要对 TLS/SSL 证书进行额外的配置，MySQL Workbench 即可连接到服务器，但建议将 TLS/SSL CA 证书与 MySQL Workbench 绑定。 有关详细信息，请参阅[使用 TLS/SSL 连接](./how-to-connect-tls-ssl.md)

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>创建表、插入数据、读取数据、更新数据、删除数据

1. 将示例 SQL 代码复制并粘贴到一个空白 SQL 选项卡中，以阐释一些示例数据。

    此代码将创建名为 quickstartdb 的空数据库，然后创建名为清单的示例表。 它会插入一些行，然后读取这些行。 它通过更新语句更改数据，并再次读取这些行。 最后，它删除一个行，并再次读取这些行。

    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;

    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);

    -- Read
    SELECT * FROM inventory;

    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;

    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    此屏幕快照显示 SQL Workbench 中的一个 SQL 代码示例以及运行该示例代码后的输出。

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="运行示例 SQL 代码的 MySQL Workbench SQL 选项卡":::

2. 若要运行示例 SQL 代码，请单击“SQL 文件”选项卡工具栏中的闪电图标。
3. 请注意页面中间“结果网格”部分中的三个选项卡式结果。
4. 请注意页面底部的“输出”列表。 显示有每个命令的状态。

现已使用 MySQL Workbench 连接到 Azure Database for MySQL 灵活服务器，并已使用 SQL 语言查询数据。

## <a name="next-steps"></a>后续步骤
- [Azure Database for MySQL 灵活服务器中使用传输层安全性 (TLS 1.2) 的加密连接](./how-to-connect-tls-ssl.md)。
- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)。
- [使用 Azure 门户创建和管理 Azure Database for MySQL 灵活服务器防火墙规则](./how-to-manage-firewall-portal.md)。
- [使用 Azure 门户创建和管理 Azure Database for MySQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-portal.md)。
