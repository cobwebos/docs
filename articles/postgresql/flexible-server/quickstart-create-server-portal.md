---
title: 快速入门：创建服务器 - Azure 门户 - Azure Database for PostgreSQL 灵活服务器
description: 使用 Azure 门户用户界面创建和管理 Azure Database for PostgreSQL 灵活服务器的快速入门指南。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 25c3f875717c9c064af0ce27bdab735db67d2f5d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943337"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建 Azure Database for PostgreSQL 灵活服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

用于 PostgreSQL 的 Azure 数据库是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 PostgreSQL 数据库。 本快速入门介绍如何使用 Azure 门户在大约五分钟内创建 Azure Database for PostgreSQL 灵活服务器。

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

打开 Web 浏览器并转到[门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

## <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器

创建的 Azure Database for PostgreSQL 服务器中包含一组已配置的[计算和存储资源](./concepts-compute-storage.md)。 将在 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)中创建服务器。

若要创建用于 PostgreSQL 的 Azure 数据库服务器，请执行以下步骤：

1. 在门户左上角选择“创建资源”(+)。

2. 选择“数据库” > “用于 PostgreSQL 的 Azure 数据库”。

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="菜单中的 Azure Database for PostgreSQL":::

3. 选择“灵活服务器”部署选项。

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="菜单中的 Azure Database for PostgreSQL":::

4. 填写“基本”表单，其中包含以下信息：

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="菜单中的 Azure Database for PostgreSQL":::

    设置|建议的值|说明
    ---|---|---
    订阅|订阅名称|要用于服务器的 Azure 订阅。 如果你有多个订阅，请选择要计费的资源所在的订阅。
    资源组|myresourcegroup| 新的资源组名称，或订阅中的现有资源组。
    服务器名称 |*mydemoserver*|用于标识用于 PostgreSQL 的 Azure 数据库服务器的唯一名称。 域名 *postgres.database.azure.com* 附加到提供的服务器名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 该名称必须至少包含 3 到 63 个字符。
    管理员用户名 |*myadmin*| 连接到服务器时使用的自己的登录帐户。 管理员登录名不能是 **azure_superuser**、**azure_pg_admin**、**admin**、**administrator**、**root**、**guest** 或 **public**， 不能以 **pg_** 开头。
    密码 |你的密码| 服务器管理员帐户的新密码。 该密码必须包含 8 到 128 个字符。 密码必须包含以下三个类别的字符：英文大写字母、英文小写字母、数字 (0 到 9)和非字母数字字符（!, $, #, % 等）。
    位置|离用户最近的区域| 最靠近用户的位置。
    版本|最新主版本| 除非另有特定的要求，否则为最新 PostgreSQL 主版本。
    计算 + 存储 | “常规用途”、“4 个 vCore”、“512 GB”和“7 天”    | 新服务器的计算、存储和备份配置。 选择“配置服务器”。 “常规用途”、“4 个 vCore”、“512 GB”和“7 天”分别是“计算层”、“vCore”、“存储”和“备份保持期”的默认值      。 可以按原样保留这些滑块，也可以对其进行调整。 若要保存此定价层选择，请选择“确定”  。 下一个屏幕截图捕获了这些选择。

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="菜单中的 Azure Database for PostgreSQL":::
    
5. 配置网络选项

    在“网络”选项卡上，可以选择访问服务器的方式。 用于 PostgreSQL 的 Azure 数据库会在服务器级别创建防火墙。 除非创建了规则来为特定的 IP 地址打开防火墙，否则此防火墙会阻止外部应用程序和工具连接到服务器和服务器上的任何数据库。 建议使服务器可公开访问：

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="菜单中的 Azure Database for PostgreSQL":::

    然后将其限制为你自己的客户端 IP 地址：

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="菜单中的 Azure Database for PostgreSQL":::

6. 选择“查看 + 创建”，查看所选内容。 选择“创建”以预配服务器。 此操作可能需要几分钟的时间。

7. 在工具栏上选择“通知”图标（铃铛）以监视部署过程。 完成部署后，可以选择“固定到仪表板”  ，以便在 Azure 门户仪表板上为此服务器创建磁贴作为到此服务器“概述”  页的快捷方式。 选择“转到资源”可打开此服务器的“概述”页。

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="菜单中的 Azure Database for PostgreSQL":::

   默认情况下，会在服务器下创建 **postgres** 数据库。 [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) 是供用户、实用工具和第三方应用程序使用的默认数据库。 （另一个默认数据库是 **azure_maintenance**， 其功能是将托管服务进程与用户操作分开。 你不能访问此数据库。）

    > [!NOTE]
    > 连接到 Azure Database for PostgreSQL 服务器时，通过端口 5432 进行通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 5432 的出站流量。 如果是这样，则无法连接到服务器，除非 IT 部门打开了端口 5432。
    >

## <a name="get-the-connection-information"></a>获取连接信息

创建用于 PostgreSQL 的 Azure 数据库服务器时，会创建名为 **postgres** 的默认数据库。 若要连接到数据库服务器，需要完整的服务器名称和管理员登录凭据。 此前可能已在本快速入门文章中记下这些值。 如果没有记下这些值，可以在门户的服务器“概览”页中轻松找到服务器名称和登录信息。

打开服务器的“概览”页。 记下“服务器名称”和“服务器管理员登录名”。 将光标悬停在每个字段，然后复制符号就会显示在文本右侧。 根据需要选择复制符号即可复制这些值。

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="菜单中的 Azure Database for PostgreSQL":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>使用 psql 连接到 PostgreSQL 数据库

可以通过多个应用程序连接到 Azure Database for PostgreSQL 服务器。 如果客户端计算机已安装 PostgreSQL，则可以使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 的本地实例连接到 Azure PostgreSQL 服务器。 现在使用 psql 命令行实用工具连接到 Azure PostgreSQL 服务器。

1. 运行以下 psql 命令连接到 Azure Database for PostgreSQL 服务器

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   例如，以下命令使用访问凭据连接到 PostgreSQL 服务器 mydemoserver.postgres.database.azure.com**** 上名为“postgres”**** 的默认数据库。 提示输入密码时，输入之前选择的 `<server_admin_password>`。
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   连接后，psql 实用工具会显示 postgres 提示符，要求在其中键入 sql 命令。 在初始连接输出中可能会显示警告，因为所使用的 psql 版本可能不同于 Azure Database for PostgreSQL 服务器版本。

   psql 输出示例：

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > 如果未将防火墙配置为允许客户端的 IP 地址，则会出现以下错误：
   >
   > psql: 致命错误: 主机 `<IP address>`、用户 "myadmin"、数据库 "postgres" 没有 pg_hba.conf 条目，SSL 出现致命错误:需要 SSL 连接。 请指定 SSL 选项，然后重试。
   >
   > 确认在上面的防火墙规则步骤中允许你的客户端 IP。

2. 出现提示时通过键入以下命令来创建名为“mypgsqldb”的空数据库：

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. 在提示符下，执行以下命令来将连接切换到新建的数据库 **mypgsqldb**：

    ```bash
    \c mypgsqldb
    ```

4. 键入 `\q`，再按 Enter 键退出 psql。

已通过 psql 连接到 Azure Database for PostgreSQL 服务器并创建了一个空用户数据库。

## <a name="clean-up-resources"></a>清理资源

可以通过两种方法之一清理在快速入门中创建的资源。 可以删除 Azure 资源组，其中包括资源组中的所有资源。 若要保持其他资源原封不动，请只删除服务器资源。

> [!TIP]
> 本教程系列中的其他快速入门教程是在本文的基础上制作的。 如果打算继续使用快速入门，请不要清除在本快速入门中创建的资源。 如果不打算继续，请在门户中执行以下步骤来删除本快速入门创建的资源。

若要删除包括新建服务器在内的整个资源组，请执行以下操作：

1. 在门户中找到自己的资源组。 在左侧菜单中，选择“资源组”。 然后选择资源组的名称，例如 myresourcegroup。

2. 在资源组页上，选择“删除”。 在文本框中输入资源组的名称（例如，示例 **myresourcegroup**），以确认删除。 选择“删除” 。

仅删除新建的服务器：

1. 在门户中找到自己的服务器（如果尚未将其打开）。 在左侧菜单中选择“所有资源”。 然后搜索所创建的服务器。

2. 在“概览”  页上，选择“删除”  。

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="菜单中的 Azure Database for PostgreSQL":::

3. 确认要删除的服务器名称并查看其下受影响的数据库。 在文本框中输入服务器名称，例如 mydemoserver。 选择“删除” 。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用应用服务和 PostgreSQL 部署 Django 应用](tutorial-django-app-service-postgres.md)
