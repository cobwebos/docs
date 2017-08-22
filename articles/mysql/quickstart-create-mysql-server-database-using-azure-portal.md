---
title: "快速入门：创建 Azure Database for MySQL 服务器 - Azure 门户 | Microsoft Docs"
description: "本文介绍如何使用 Azure 门户在大约五分钟内快速创建示例 Azure Database for MySQL 服务器。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: zh-cn
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>使用 Azure 门户创建 Azure Database for MySQL 服务器
Azure Database for MySQL 是一种托管服务，可用于在云中运行、管理和缩放高可用性的 MySQL 数据库。 此快速入门介绍了如何使用 Azure 门户在大约五分钟内创建 Azure Database for MySQL 服务器。 

如果还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-azure"></a>登录 Azure
打开 Web 浏览器，导航到 [Microsoft Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

## <a name="create-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器
创建 Azure Database for MySQL 服务器时，会使用定义好的一组[计算和存储资源](./concepts-compute-unit-and-storage.md)。 将在 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)中创建服务器。

按照以下步骤创建 Azure Database for MySQL 服务器：

1. 单击 Azure 门户左上角的“新建”按钮 (+)。

2. 从“新建”页中选择“数据库”，然后从“数据库”页中选择“Azure Database for MySQL”。 还可以在“新建”页搜索框中键入“MySQL”以查找该服务。
![Azure 门户 - 新建 - 数据库 - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. 如上图所示，在新服务器详细信息窗体中填写以下信息：

    **设置** | 建议的值 | **字段说明** 
    ---|---|---
    服务器名称 | myserver4demo | 选择用于标识 Azure Database for MySQL 服务器的唯一名称。 域名 mysql.database.azure.com 附加到可供应用程序连接到的服务器名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符，并且必须包含 3 到 63 个字符。
    订阅 | 你的订阅 | 要用于服务器的 Azure 订阅。 如果有多个订阅，请选择要计费的资源所在的相应订阅。
    资源组 | myresourcegroup | 可以创建新的资源组名称，也可以使用订阅中现有的。
    服务器管理员登录名 | myadmin | 创建自己的登录帐户，在连接到服务器时使用。 管理员登录名不能是“azure_superuser”、“admin”、“administrator”、“root”、“guest”或“public”。
    密码 | *由用户选择* | 为服务器管理员帐户创建新密码。 必须包含 8 到 128 个字符。 密码必须含以下字符类别中的三类 – 英文大写字母、英文小写字母、数字(0-9)及非字母数字字符（!、$、#、% 等）。
    确认密码 | *由用户选择*| 确认管理员帐户密码。
    位置 | 
            *离用户最近的区域*| 选择最靠近用户或其他 Azure 应用程序的位置。
    版本 | 选择最新版本| 选择最新版本，有特定要求的除外。
    定价层 | 基本，50 个计算单元，50 GB | 单击“定价层”为新数据库指定服务层和性能级别。 在顶部的选项卡中选择“基本层”。 单击“计算单位”滑块的左端，将值调整为适用于本快速入门的最小值。 单击“确定”，保存所做的定价层选择。 请参阅下面的屏幕截图。
    固定到仪表板 | 勾选标记 | 选中“固定到仪表板”选项，以便轻松地在 Azure 门户的前端仪表板页上跟踪服务器。

    > [!IMPORTANT]
    > 在此处指定的服务器管理员登录名和密码是以后在本快速入门中登录到服务器及其数据库所必需的。 请牢记或记录此信息，以后会使用到它。
    > 

    ![Azure 门户 - 通过提供所需的窗体输入创建 MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  单击“创建”以预配服务器。 预配需要数分钟，最多需要 20 分钟。
   
5.  在工具栏上，单击“通知”（钟形图标）可监视部署过程。

## <a name="configure-a-server-level-firewall-rule"></a>配置服务器级防火墙规则

Azure Database for MySQL 服务在服务器级别创建防火墙。 除非创建了防火墙规则来为特定的 IP 地址打开防火墙，否则此防火墙会阻止外部应用程序和工具连接到服务器和服务器上的任何数据库。 

1.  部署完成后，找到服务器。 可以根据需要进行搜索。 例如，单击左侧菜单中的“所有资源”并键入服务器名称（例如 myserver4demo）即可搜索新创建的服务器。 单击搜索结果中列出的服务器名称。 服务器的“概述”页面随即打开，其中提供了用于进一步配置的选项。

2. 在服务器页中，选择“连接安全性”。

3.  在“防火墙规则”标题下，单击“规则名称”列中的空白文本框，开始创建防火墙规则。 

    对于本快速入门，请在每个列的文本框中填充以下值，启用到服务器的所有 IP 地址：

    规则名称 | 起始 IP | 结束 IP 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. 在“连接安全性”页的上部工具栏中，单击“保存”。 稍等片刻，在通知显示已成功更新连接安全性后，再继续操作。

    > [!NOTE]
    > 连接到 Azure Database for MySQL 时，经端口 3306 进行通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 3306 的出站流量。 如果是这样，则无法连接到服务器，除非 IT 部门打开了端口 3306。
    > 

## <a name="get-the-connection-information"></a>获取连接信息
若要连接到数据库服务器，需牢记完整的服务器名称和管理员登录凭据。 你可能已在此前的快速入门文章中记下这些值。 如果没有记下这些值，可以在 Azure 门户的服务器“概览”页或“属性”页中轻松地找到服务器名称和登录信息。

1. 打开服务器的“概览”页。 记下“服务器名称”和“服务器管理员登录名”。 
    将光标悬停在每个字段，然后复制图标就会显示在文本右侧。 根据需要单击复制图标即可复制这些值。

    在此示例中，服务器名称是 myserver4demo.mysql.database.azure.com，服务器管理员登录名是 myadmin@myserver4demo。

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>使用 mysql 命令行工具连接到 MySQL
可以通过多个应用程序连接到 Azure Database for MySQL 服务器。 让我们先使用 [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) 命令行工具来演示如何连接到该服务器。  可以根据此处所述使用 Web 浏览器和 Azure Cloud Shell，不需安装任何其他软件。 如果已在自己的计算机上通过本地方式安装了 mysql 实用工具，也可从该处进行连接。

1. 通过 Azure 门户网页右上角的终端图标 ( >_ ) 启动 Azure Cloud Shell。

2. Azure Cloud Shell 会在浏览器中打开，并允许键入 bash shell 命令。

    ![命令提示符 - mysql 命令行示例](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. 在 Cloud Shell 绿色提示符下键入 mysql 命令行，连接到 Azure Database for MySQL 服务器。

    可以借助 mysql 实用工具，使用以下格式连接到 Azure Database for MySQL 服务器：
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    例如，以下命令连接到示例服务器：
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    mysql 参数 |建议的值|说明
    ---|---|---
    --host | 服务器名称 | 指定此前在创建 Azure Database for MySQL 时使用过的服务器名称值。 显示的示例服务器为 myserver4demo.mysql.database.azure.com。 请使用完全限定的域名 (\*.mysql.database.azure.com)，如示例中所示。 如果记不起服务器名称，请按上一部分的步骤操作，以便获取连接信息。 
    --user | 服务器管理员登录名 |键入此前在创建 Azure Database for MySQL 时提供的服务器管理员登录用户名。 如果记不起用户名，请按上一部分的步骤操作，以便获取连接信息。  格式为 username@servername。
    --password | 等待系统提示 | 输入命令以后，系统会提示你“输入密码”。 如果系统提示，请键入在创建服务器时提供的密码。  注意，键入密码字符时，这些字符不会显示在 bash 提示符处。 在键入所有字符后按 Enter，以便进行身份验证和连接。

   连接后，mysql 实用工具会显示 `mysql>` 提示符，提示你键入命令。 

    mysql 输出示例：
    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > 如果未将防火墙配置为允许 Azure Cloud Shell 的 IP 地址，则会出现以下错误：
    >
    > 错误 2003 (28000): 不允许 IP 地址为 123.456.789.0 的客户端访问服务器。
    >
    > 若要解决此错误，请确保服务器配置符合本文“配置服务器级防火墙规则”部分相关步骤的要求。

4. 查看服务器状态，确保连接正常。 连接后，在 mysql > 提示符处键入 `status`。
    ```sql
    status
    ```

   > [!TIP]
   > 有关其他命令，请参阅 [MySQL 5.7 参考手册 - 第 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)。

5.  在 mysql> 提示符处键入以下命令，创建空数据库：
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    该命令可能需要一定的时间才能完成。 

    在 Azure Database for MySQL 数据库中，可创建一个或多个数据库。 可以选择为每个服务器创建单一数据库来使用所有资源，还可以创建多个数据库来共享资源。 可以创建的数据库数目没有限制，但多个数据库共享相同的服务器资源。 

6. 通过在 mysql > 提示符处键入以下命令来列出数据库：

    ```sql
    SHOW DATABASES;
    ```

7.  键入 `\q` 后按 Enter 即可退出 mysql 工具。 完成后即可关闭 Azure Cloud Shell。

现在，你已连接到 Azure Database for MySQL 并创建空白用户数据库。 请转到下一部分，重复一个类似的练习，使用另一工具（即 MySQL Workbench）连接到同一服务器。

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>使用 MySQL Workbench GUI 工具连接到服务器
若要使用 GUI 工具 MySQL Workbench 连接到 Azure MySQL 服务器，请执行以下操作：

1.  启动客户端计算机上的 MySQL Workbench 应用程序。 可以从[此处](https://dev.mysql.com/downloads/workbench/)下载并安装 MySQL Workbench。

2.  在“设置新连接”对话框的“参数”选项卡上，输入以下信息：

    ![设置新连接](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **设置** | 建议的值 | **字段说明** |
    |---|---|---|
    |   连接名称 | 演示连接 | 指定此连接的标签。 |
    | 连接方法 | 标准 (TCP/IP) | 标准 (TCP/IP) 就足够了。 |
    | 主机名 | 服务器名称 | 指定此前在创建 Azure Database for MySQL 时使用过的服务器名称值。 显示的示例服务器为 myserver4demo.mysql.database.azure.com。 请使用完全限定的域名 (\*.mysql.database.azure.com)，如示例中所示。 如果记不起服务器名称，请按上一部分的步骤操作，以便获取连接信息。  |
    | 端口 | 3306 | 在连接到 Azure Database for MySQL 时，始终使用端口 3306。 |
    | 用户名 |  服务器管理员登录名 | 键入此前在创建 Azure Database for MySQL 时提供的服务器管理员登录用户名。 示例用户名为 myadmin@myserver4demo。 如果记不起用户名，请按上一部分的步骤操作，以便获取连接信息。 格式为 username@servername。
    | 密码 | 你的密码 | 单击“在保管库中存储...”按钮来保存密码。 |

    单击“测试连接”以测试是否所有参数均已正确配置。 单击“确定”保存连接。 

    > [!NOTE]
    > 默认情况下，SSL 是在服务器上强制实施的，并且它需要额外配置才能成功连接。 请参阅[配置应用程序的 SSL 连接性以安全连接到 Azure Database for MySQL](./howto-configure-ssl.md)。  若要对此快速入门禁用 SSL，请访问 Azure 门户，然后单击“连接安全性”页来禁用“强制实施 SSL 连接”切换按钮。

## <a name="clean-up-resources"></a>清理资源
清理快速入门中创建的资源时，可以删除 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)，包括删除资源组中的所有资源，也可以删除单服务器资源，其他资源原封不动。

> [!TIP]
> 本教程系列中的其他快速入门教程是在本文的基础上制作的。 如果打算继续使用后续的快速入门，请不要清除在本快速入门中创建的资源。 如果不打算继续，请在 Azure 门户中执行以下步骤来删除此快速入门创建的所有资源。
>

若要删除包括新建服务器在内的整个资源组，请执行以下操作：
1.  在 Azure 门户中查找资源组。 在 Azure 门户的左侧菜单中单击“资源组”，然后单击资源组的名称，例如 myresourcegroup 示例。
2.  在资源组页上，单击“删除”。 然后在文本框中键入确认删除的资源组的名称（例如 myresourcegroup 示例），再单击“删除”。

或者，若要改为删除新创建的服务器，请执行以下操作：
1.  在 Azure 门户中找到服务器（如果尚未将其打开）。 在 Azure 门户的左侧菜单中单击“所有资源”，然后搜索所创建的服务器。
2.  在“概览”页上，单击顶部窗格的“删除”按钮。
![Azure Database for MySQL - 删除服务器](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  确认要删除的服务器名称并显示其下受影响的数据库。 在文本框中键入服务器名称（例如 myserver4demo），然后单击“删除”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设计第一个 Azure Database for MySQL 数据库](./tutorial-design-database-using-portal.md)


