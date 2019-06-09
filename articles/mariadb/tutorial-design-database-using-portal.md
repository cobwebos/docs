---
title: 教程：使用 Azure 门户设计 Azure Database for MariaDB
description: 本教程介绍如何使用 Azure 门户创建和管理 Azure Database for MariaDB 服务器和数据库。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: tutorial
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: 1938a84d3e9c0ba8c84cbdbd2eee5b7ca448554d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515661"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>教程：使用 Azure 门户设计 Azure Database for MariaDB 数据库

Azure Database for MariaDB 是一种托管服务，可用于在云中运行、管理和缩放高可用性的 MySQL 数据库。 使用 Azure 门户可以轻松管理服务器和设计数据库。

本教程介绍如何使用 Azure 门户完成以下操作：

> [!div class="checklist"]
> * 创建 Azure Database for MariaDB
> * 配置服务器防火墙
> * 使用 mysql 命令行工具创建数据库
> * 加载示例数据
> * 查询数据
> * 更新数据
> * 还原数据

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在浏览器中转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

## <a name="create-an-azure-database-for-mariadb-server"></a>创建 Azure Database for MariaDB 服务器

创建 Azure Database for MariaDB 服务器时，请使用定义好的一组[计算和存储资源](concepts-pricing-tiers.md)。 将在 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中创建服务器。

1. 选择门户左上角的“创建资源”按钮 (+)  。

2. 选择“数据库” > “Azure Database for MariaDB”   。 还可以在搜索框中键入“MariaDB”以查找该服务  。
   
   ![转到 MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. 选择“Azure Database for MariaDB”  磁贴，然后选择“创建”  。 输入或选择所需信息。
   
   ![创建窗体](./media/tutorial-design-database-using-portal/2-create-form.png)

    设置 | 建议的值 | 字段说明 
    ---|---|---
    服务器名称 |  唯一的服务器名称 | 选择用于标识 Azure Database for MariaDB 服务器的唯一名称。 例如，**mydemoserver**。 域名 *.mariadb.database.azure.com* 将追加到所输入的服务器名称后面。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 它必须包含 3 到 63 个字符。
    订阅 | *用户的订阅* | 选择要用于服务器的 Azure 订阅。 如果有多个订阅，请选择要计费的资源所在的订阅。
    资源组 |  myresourcegroup | 输入新的资源组名称，或选择现有的资源组。
    选择源 | **空白** | 选择“空白”  即可创建新服务器。 （如果要从现有 Azure Database for MariaDB 服务器的异地备份创建服务器，请选择“备份”  ）。
    服务器管理员登录名 | **myadmin** | 连接到服务器时需要使用的登录帐户。 管理员登录名不能是“azure_superuser”、“admin”、“administrator”、“root”、“guest”或“public”。      
    密码 |  由用户选择 | 为服务器管理员帐户输入新密码。 该密码必须包含 8 到 128 个字符。 密码必须包含以下三个类别的字符：英文大写字母、英文小写字母、数字 (0-9)和非字母数字字符（!, $, #, % 等）。
    确认密码 |  由用户选择| 确认管理员帐户密码。
    位置 |  离用户最近的区域| 选择最靠近用户或其他 Azure 应用程序的位置。
    版本 |  最新版本| 最新版本，有特定要求（即要求使用其他版本）的除外。
    定价层 | 请参阅说明。 | 新服务器的计算、存储和备份配置。 选择“定价层”   >   “常规用途”。 为以下设置保留默认值：<br><ul><li>**计算代系**（第 5 代）</li><li>**vCore**（4 个 vCore）</li><li>**存储空间** (100 GB)</li><li>**备份保持期**（7 天）</li></ul><br>若要在异地冗余存储中启用服务器备份，请选择“异地冗余”作为“备份冗余选项”   。 <br><br>若要保存此定价层选择，请选择“确定”  。 下一个屏幕截图捕获了这些选择。
    
   ![定价层](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

   > [!TIP]
   > 启用“自动增长”  后，当接近分配的限制时，服务器会增加存储空间，而不会影响工作负荷。

4. 选择“创建”  。 一两分钟后，新的 Azure Database for MariaDB 服务器将在云中运行。 若要监视部署过程，请在工具栏上选择“通知”。 

## <a name="configure-the-firewall"></a>配置防火墙

Azure Database for MariaDB 受防火墙保护。 默认情况下，将拒绝与服务器和服务器内数据库的所有连接。 首次连接到 Azure Database for MariaDB 之前，请配置防火墙以添加客户端计算机的公共网络 IP 地址（或 IP 地址范围）。

1. 选择新创建的服务器，然后选择“连接安全性”  。
   
   ![连接安全性](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. 可以选择“添加我的 IP”  或在此处配置防火墙规则。 创建规则后请记得选择“保存”  。

现在可以使用 mysql 命令行工具或 MySQL Workbench 来连接到服务器。

> [!TIP]
> Azure Database for MariaDB 服务器通过端口 3306 进行通信。 如果尝试从企业网络内部进行连接，则网络的防火墙可能不允许经端口 3306 的出站流量。 这种情况下，若要连接到Azure Database for MariaDB 服务器，必须要求 IT 部门打开端口 3306。

## <a name="get-connection-information"></a>获取连接信息

从 Azure 门户获取 Azure Database for MariaDB 服务器的“服务器名称”（完全限定）和“服务器管理员登录名”   。 使用 mysql 命令行工具通过完全限定的服务器名称连接到服务器。 

1. 在 [Azure 门户](https://portal.azure.com/)的左侧菜单中，选择“所有资源”。  输入服务器名称，搜索 Azure Database for MariaDB 服务器。 选择服务器名称以查看服务器详细信息。

2. 在“概览”页中记下“服务器名称”和“服务器管理员登录名”的值。    也可选择每个字段旁边的**复制**按钮，将值复制到剪贴板。

   ![服务器属性](./media/tutorial-design-database-using-portal/2-server-properties.png)

在我们的示例中，服务器名称是 mydemoserver.mariadb.database.azure.com  ，服务器管理员登录名是 myadmin\@mydemoserver  。

## <a name="connect-to-the-server-by-using-mysql"></a>通过 mysql 连接到服务器

使用 [mysql 命令行工具](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)建立与 Azure Database for MariaDB 服务器的连接。 可以通过 Azure Cloud Shell 在浏览器中运行 mysql 命令行工具，也可以使用本地安装的 mysql 工具在计算机上运行。 若要打开 Azure Cloud Shell，请选择本文中代码块上的“试用”按钮，或访问 Azure 门户并单击右上角工具栏中的 **>_** 图标。  

输入进行连接的命令：

```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>创建空数据库

连接到服务器后，请创建一个要使用的空数据库：

```sql
CREATE DATABASE mysampledb;
```

出现提示时，请运行以下命令，将连接切换到新建的数据库：

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>在数据库中创建表

了解如何连接到 Azure Database for MariaDB 数据库以后，可以完成一些基本任务。

首先，创建表并加载一些数据。 创建一个存储清单信息的表：

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>将数据加载到表

表创建好后，可向其插入一些数据。 在打开的命令提示符窗口中运行以下查询，插入几行数据：

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>查询和更新表中的数据

运行以下查询，从数据库表中检索信息：

```sql
SELECT * FROM inventory;
```

还可以更新表中的数据：

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

检索数据时行也会进行更新：

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>将数据库还原到以前的时间点

假设你意外删除了一个重要的数据库表，并且无法轻松地恢复数据。 在 Azure Database for MariaDB 中，可以将服务器还原到某个时间点，方法是在新服务器上创建数据库的副本。 可以使用此新服务器恢复已删除的数据。 以下步骤将示例服务器还原到添加此表之前的时间点：

1. 在 Azure 门户中，找到 Azure Database for MariaDB。 在“概览”页上，选择“还原”。  

   ![还原数据库](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. 在“还原”页上，输入或选择以下信息： 
   
   ![“还原”窗体](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **还原点**：在列出的时间范围内选择要还原到的时间点。 请确保将本地时区转换为 UTC。
   - **还原到新服务器**：输入一个要还原到的新服务器名称。
   - **位置**：此区域与源服务器相同，不能更改。
   - **定价层**：定价层与源服务器相同，不能更改。
   
3. 选择“确定”，将服务器还原到删除该表之前的某个时间点[还原到某个时间点](./howto-restore-server-portal.md)。  还原服务器时，将创建服务器在所选时间点的新副本。 

## <a name="next-steps"></a>后续步骤
本教程介绍如何使用 Azure 门户完成以下操作：

> [!div class="checklist"]
> * 创建 Azure Database for MariaDB
> * 配置服务器防火墙
> * 使用 mysql 命令行工具创建数据库
> * 加载示例数据
> * 查询数据
> * 更新数据
> * 还原数据

> [!div class="nextstepaction"]
> [如何将应用程序连接到 Azure Database for MariaDB](./howto-connection-string.md)
