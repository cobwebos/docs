---
title: "设计你的第一个 Azure Database for MySQL 数据库 - Azure 门户 | Microsoft Docs"
description: "本教程介绍如何使用 Azure 门户创建和管理 Azure Database for MySQL 服务器。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2958486a0ec055cf1fe334e97389536b2c2bb01f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="design-your-first-azure-database-for-mysql-database"></a>设计你的第一个 Azure Database for MySQL 数据库

Azure Database for MySQL 是一种托管服务，可用于在云中运行、管理和缩放高可用性的 MySQL 数据库。 使用 Azure 门户可以轻松管理服务器和设计数据库。

本教程介绍如何使用 Azure 门户完成以下操作：

> [!div class="checklist"]
> * 创建 Azure Database for MySQL
> * 配置服务器防火墙
> * 使用 [mysql 命令行工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)创建数据库
> * 加载示例数据
> * 查询数据
> * 更新数据
> * 还原数据

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户
打开最喜爱的 Web 浏览器，然后访问 [Microsoft Azure 门户](https://portal.azure.com/)。 输入凭据以登录门户。 默认视图是服务仪表板。

## <a name="create-an-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器
创建 Azure Database for MySQL 服务器时，会使用定义好的一组[计算和存储资源](./concepts-compute-unit-and-storage.md)。 将在 [Azure 资源组](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)中创建服务器。

1.    导航到“数据库” -> “MySQL”。 如果在“数据库”类别下找不到 MySQL 服务器，请单击“查看全部”显示所有可用的数据库服务。 还可以在搜索框中键入“MySQL”快速查找服务。
![2-1 导航到 MySQL](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2.    单击“MySQL”图标，然后单击“创建”。

在示例中，用以下信息填写 Azure Database for MySQL 窗体：

| 窗体字段 | 字段说明 |
|----------------|-----------------------|
| *服务器名称* | mysqlserver4demo（服务器名称是全局唯一的） |
| *订阅* | MySQLaaS（从下拉列表中选择） |
| *资源组* | myresource（创建一个资源组或使用现有资源组） |
| 服务器管理员登录名 | myadmin（设置管理员帐户名称） |
| *密码* | 设置管理员帐户密码 |
| *确认密码* | 确认管理员帐户密码 |
| *位置* | 北欧（选择“北欧”或“美国西部”） |
| *版本* | 5.6（选择 MySQL 服务器版本） |
| 配置性能 | 基本（选择“性能层”、“计算单元”、“存储”，然后单击“确定”） |

然后单击“创建”。 一两分钟后，新 Database for MySQL 服务器将在云中运行。 可单击工具栏上的“通知”按钮来监视部署过程。

> [!TIP]
> 建议将 Azure 服务放在同一区域，并选择离自己最近的位置。 此外，可选中“固定到仪表板”选项，轻松跟踪部署。

![2-2 创建服务器](./media/tutorial-design-database-using-portal/2_2-Create-server.png)

## <a name="configure-firewall"></a>配置防火墙
Azure Databases for MySQL 受防火墙保护。 默认情况下，将拒绝与服务器和服务器内数据库的所有连接。 首次从客户端连接到 Azure Database for MySQL 之前，必需配置防火墙并将客户端的公共网络 IP 地址（或 IP 地址范围）添加到允许列表。

1.    单击新创建的服务器，然后单击“连接安全性”。

![3-1 连接安全性](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)

2.    可以“添加我的 IP”或在此处配置防火墙规则。 创建规则后请记得单击“保存”。

现在可以使用 mysql 命令行工具或 MySQL Workbench GUI 工具连接服务器。

> Azure Database for MySQL 通过端口 3306 进行通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 3306 的出站流量。 如果是这样，则将无法连接到 Azure MySQL 服务器，除非 IT 部门打开了端口 3306。

## <a name="get-connection-information"></a>获取连接信息
请在 Azure 门户中获取 Azure MySQL 服务器的完全限定服务器名称。 请使用 mysql 命令行工具通过完全限定的服务器名称连接到服务器。

1.    在 [Azure 门户](https://portal.azure.com/)中，单击左侧菜单中的“所有资源”，然后单击 Azure Database for MySQL 服务器。

2.    单击“属性”。 记下“服务器名称”和“服务器管理员登录名”。
![4-2 服务器属性](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

在此示例中，服务器名称是 mysql4doc.database.windows.net，服务器管理员登录名是 mysqladmin@mysql4doc。

## <a name="connect-to-the-server-using-mysql"></a>使用 mysql 连接服务器
使用 [mysql 命令行工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)建立与 Azure Database for MySQL 服务器的连接。 在此示例中，该命令是：
```cmd
mysql -h mysqlserver4demo.database.windows.net -u myadmin@mysqlserver4demo -p
```

## <a name="create-a-blank-database"></a>创建空数据库
连接到服务器后，立即创建一个要使用的空数据库。
```sql
CREATE DATABASE mysampledb;
```

出现提示时，请运行以下命令，切换为连接此新建的数据库：
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>在数据库中创建表
现已介绍了如何连接 Azure Database for MySQL 数据库，接下来将演示如何完成一些基本任务。

首先，创建表并加载一些数据。 创建一个存储清单信息的表。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>将数据加载到表
创建表后，可以向其中插入一些数据。 在打开的命令提示窗口中，运行以下查询来插入几行数据。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

现已将两行示例数据加载到了之前创建的表中。

## <a name="query-and-update-the-data-in-the-tables"></a>查询和更新表中的数据
执行以下查询，从数据库表中检索信息。
```sql
SELECT * FROM inventory;
```

还可以更新表中的数据。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

检索数据时行也会相应进行更新。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>将数据库还原到以前的时间点
假设意外删除了此表。 这是你不能轻易还原的内容。 借助 Azure Database for MySQL，可返回到最近 35 天内的任意时间点并将此时间点还原到新的服务器。 可以使用此新服务器恢复已删除的数据。 以下步骤将示例服务器还原到添加此表之前的时间点。

1 - 在服务器的 Azure Database for MySQL 页中，单击工具栏上的“还原”。 将打开“还原”页面。
![10-1 还原数据库](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2- 使用必需信息填写“还原”窗体：

-    还原点：选择更改服务器前的时间点。
-    目标服务器：提供一个要还原到的新服务器名称。
-    位置：不能选择区域，此区域默认与源服务器相同。
-    定价层：还原服务器时不能更改此值。 此值与源服务器相同。
![10-2 还原窗体](./media/tutorial-design-database-using-portal/10_2-restore-form.png)

3- 单击“确定”，将服务器[还原到删除这些表之前的时间点](./howto-restore-server-portal.md)。 将服务器还原到不同的时间点会在指定时间点（前提是在服务层保留时间段内）创建原始服务器的一个备份新服务器。

## <a name="next-steps"></a>后续步骤

本教程介绍如何使用 Azure 门户完成以下操作：

> [!div class="checklist"]
> * 创建 Azure Database for MySQL
> * 配置服务器防火墙
> * 使用 [mysql 命令行工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)创建数据库
> * 加载示例数据
> * 查询数据
> * 更新数据
> * 还原数据

[使用 Azure 门户创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-portal.md)

