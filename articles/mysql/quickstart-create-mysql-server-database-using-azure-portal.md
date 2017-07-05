---
title: "快速入门：创建 Azure Database for MySQL 服务器 - Azure 门户 | Microsoft Docs"
description: "本文介绍如何使用 Azure 门户在大约五分钟内快速创建示例 Azure Database for MySQL 服务器。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: hero-article
ms.date: 06/14/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 3e0aa1ecccdb55b75dd332818e09b21431716fdc
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>使用 Azure 门户创建 Azure Database for MySQL 服务器
本文介绍如何使用 Azure 门户在大约五分钟内创建 Azure Database for MySQL 服务器。 

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-azure"></a>登录 Azure
打开 Web 浏览器，导航到 [Microsoft Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

## <a name="create-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器
1. 单击 Azure 门户左上角的“新建”按钮。

2. 从“新建”页中选择“数据库”，然后从“数据库”页中选择“Azure Database for MySQL”。 还可以在“新建”页搜索框中键入“MySQL”以查找该服务。
![Azure 门户 - 新建 - 数据库 - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. 如上图所示，在新服务器详细信息窗体中填写以下信息：

| **设置** | 建议的值 | **字段说明** |
|---|---|---|
| *服务器名称* | myserver4demo  | 服务器名称必须全局唯一。 |
| *订阅* | mysubscription | 从下拉列表中选择订阅。 |
| *资源组* | myresourcegroup | 创建资源组或使用现有资源组。 |
| 服务器管理员登录名 | myadmin | 指定 MySQL 引擎中的管理员帐户名。 |
| *密码* |  | 设置强度高的管理员帐户密码。 |
| *确认密码* |  | 确认管理员帐户密码。 |
| *位置* |  | 选择可用区域。 |
| *版本* | 5.7 | 选择最新版本。 |
| 定价层 | 基本，50 个计算单元，50 GB 存储  | 选择“定价层”、“计算单元”、“存储 (GB)”，然后单击“确定”。 |
| 固定到仪表板 | 勾选标记 | 建议选中此框，以便后期能轻松找到此服务器 |

   单击“定价层”为新数据库指定定价层和性能级别。 对于此快速入门，选择“基本”层、“50 个计算单元”，以及“50 GB”存储空间。 然后单击“确定”，保存此定价层。
   
   ![Azure 门户 - 通过提供所需的窗体输入创建 MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

   然后单击“创建”。 一两分钟后，新 Azure Database for MySQL 服务器将在云中运行。 单击工具栏上的“通知”按钮（钟形图标）来监视部署过程。

## <a name="configure-the-firewall"></a>配置防火墙
首次连接到 Azure Database for MySQL 之前，请配置防火墙并将客户端的公共网络 IP 地址（或范围）添加到白名单。

1. 部署完成后，请单击左侧菜单中的“所有资源”，并键入名称“myserver4demo”来搜索新创建的服务器。 单击搜索结果中列出的服务器名称。 服务器的“概述”页面随即打开，其中提供了用于进一步配置的选项。

2. 在服务器边栏选项卡中，选择“连接安全性”。

3. 单击“添加我的 IP”添加本地计算机的 IP 地址，也可配置 IP 地址范围。 创建规则后请记得单击“保存”。
  ![Azure 门户 - 添加防火墙规则并保存](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>获取连接信息
在 Azure 门户中获取 Azure MySQL 服务器的完全限定域名。 请使用 mysql.exe 命令行工具通过完全限定的域名连接到服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，单击左侧菜单中的“所有资源”，然后单击 Azure Database for MySQL 服务器。

2.  单击“属性”。 记下“服务器名称”和“服务器管理员登录名”。
在此示例中，服务器名称是 myserver4demo.mysql.database.azure.com，服务器管理员登录名是 myadmin@myserver4demo。

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>使用 mysqlexe 命令行工具连接到服务器
使用 [mysql 命令行工具](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)建立与 Azure Database for MySQL 服务器的连接。 可以通过 Azure Cloud Shell 在浏览器中运行 mysql 命令行工具，还可以使用本地安装的 mysql 工具在自己的计算机上启动 Azure Cloud Shell。 若要启动 Azure Cloud Shell，请单击本文中代码块上的 `Try It` 按钮，或访问 [Azure 门户](https://portal.azure.com)并单击右上角工具栏中的 `>_` 图标。 

1. 键入命令以建立连接：
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. 查看服务器状态，确保连接正常。 连接后，在 mysql > 提示符处键入 `status`。
```sql
status
```

   ![命令提示符 - mysql 命令行示例](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

   > [!TIP]
   > 有关其他命令，请参阅 [MySQL 5.7 参考手册 - 第 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)。

3. 通过在 mysql > 提示符处键入 `CREATE DATABASE` 命令来创建空数据库。

   ```sql
   CREATE DATABASE quickstartdb;
   ```

   用户在 Azure Database for MySQL 数据库中可具有一个或多个数据库。 可以选择为每个服务器创建单一数据库来使用所有资源，还可以创建多个数据库来共享资源。 可以创建的数据库数目没有限制，但多个数据库共享相同的服务器资源。  

4. 通过在 mysql > 提示符处键入 `SHOW DATABASES` 命令来列出数据库。

   ```sql
   SHOW DATABASES;
   ```

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>使用 MySQL Workbench GUI 工具连接到服务器
1.  启动客户端计算机上的 MySQL Workbench 应用程序。 可以从[此处](https://dev.mysql.com/downloads/workbench/)下载并安装 MySQL Workbench。

2.  在“设置新连接”对话框的“参数”选项卡上，输入以下信息：

   ![设置新连接](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

| **设置** | 建议的值 | **字段说明** |
|---|---|---|
|   *连接名称* | 演示连接| 指定此连接的标签。 |
| *连接方法* | 标准 (TCP/IP) | 标准 (TCP/IP) 就足够了。 |
| *主机名* | myserver4demo.mysql.database.azure.com | 为服务器使用完全限定的服务器名称。 |
| *端口* | 3306 | 使用默认端口 3306。 |
| *用户名* | myadmin@myserver4demo  | 使用之前用 @ 字符标注的服务器管理员登录名和服务器名称。 |
| *密码* | 你的密码 | 单击“在保管库中存储...”按钮来保存密码。 |

单击“测试连接”以测试是否所有参数均已正确配置。 单击“确定”保存连接。 

> [!NOTE]
> 默认情况下，SSL 是在服务器上强制实施的，并且它需要额外配置才能成功连接。 请参阅[配置应用程序中的 SSL 连接性以安全连接到 Azure Database for MySQL](./howto-configure-ssl.md)。  如果想对此快速入门禁用 SSL，请访问 Azure 门户，然后单击“连接安全”页来禁用“强制实施 SSL 连接”切换按钮。

## <a name="clean-up-resources"></a>清理资源
可以通过删除 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)来清除你在此快速入门中创建的所有资源。

> [!TIP]
> 本教程系列中的其他快速入门教程是在本文的基础上制作的。 如果你打算继续使用后续的快速入门，请不要清除在本快速入门中创建的资源。 如果不打算继续，请在 Azure 门户中执行以下步骤来删除此快速入门创建的所有资源。

1.  在 Azure 门户的左侧菜单中，单击“资源组”，然后单击“myresourcegroup”。
2.  在资源组页上单击“删除”，在文本框中键入 **myresourcegroup**，然后单击“删除”。

如果要删除新创建的服务器，请执行以下操作：
1.  在 Azure 门户中的左侧菜单中，单击“PostgreSQL 服务器”，然后搜索刚创建的服务器
2.  在“概述”页面上，单击顶部窗格的“删除”按钮 ![Azure Database for MySQL - 删除服务器](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  确认要删除的服务器名称并显示其下受影响的数据库。 在文本框中键入“myserver4demo”，然后单击“删除”。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设计你的第一个 Azure Database for MySQL 数据库](./tutorial-design-database-using-portal.md)


