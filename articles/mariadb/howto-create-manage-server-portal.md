---
title: 使用 Azure 门户创建和管理 Azure Database for MariaDB 服务器
description: 本文介绍如何使用 Azure 门户快速创建新的 Azure Database for MariaDB 服务器和管理服务器。
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 5aae3eb0582956ccb45cc41d8400f489f1077bad
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143435"
---
# <a name="create-and-manage-azure-database-for-mariadb-server-using-azure-portal"></a>使用 Azure 门户创建和管理 Azure Database for MariaDB 服务器
本主题介绍如何快速创建新的 Azure Database for MariaDB 服务器。 它还提供了有关如何使用 Azure 门户来管理服务器的信息。 服务器管理包括查看服务器详细信息和数据库、重置密码、缩放资源以及删除服务器。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户
登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-azure-database-for-mariadb-server"></a>创建 Azure Database for MariaDB 服务器
按照以下步骤创建一个名为 "mydemoserver" 的 Azure Database for MariaDB 服务器。

1. 单击 Azure 门户左上角的“创建资源”按钮。

2. 在 "新建" 页上, 选择 "**数据库**", 然后在 "数据库" 页上, 选择**Azure Database for MariaDB**。

    > 使用定义的一组[计算和存储](./concepts-pricing-tiers.md)资源创建 Azure Database for MariaDB 服务器。 数据库在 Azure 资源组和 Azure Database for MariaDB 服务器中创建。

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. 使用以下信息填写 Azure Database for MariaDB 窗体:

    | **窗体字段** | **字段说明** |
    |----------------|-----------------------|
    | *服务器名称* | mydemoserver（服务器名称是全局唯一的） |
    | *订阅* | mysubscription（从下拉菜单中选择） |
    | *资源组* | myresourcegroup（新建一个资源组或使用现有资源组） |
    | *选择源* | 空白 (创建空白 MariaDB 服务器) |
    | 服务器管理员登录名 | myadmin（设置管理员帐户名称） |
    | *密码* | 设置管理员帐户密码 |
    | *确认密码* | 确认管理员帐户密码 |
    | *Location* | 东南亚（在“北欧”和“美国西部”之间选择） |
    | *版本* | 10.3 (选择 Azure Database for MariaDB 服务器版本) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. 单击 "**配置服务器**", 为新服务器指定服务层和性能级别。 选择“常规用途”选项卡。“第 5 代”、“4 个 vCore”、“100 GB”和“7 天”分别是“计算代系”、“vCore”、“存储”和“备份保持期”的默认值。 您可以将这些滑杆保留原样。 若要在异地冗余存储中启用服务器备份，请从“备份冗余选项”中选择“异地冗余”。

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. 单击 "**查看 + 创建**" 以切换到 "查看" 屏幕并验证所有详细信息。 单击“创建”以预配服务器。 预配需要数分钟。

    > 选择“固定到仪表板”选项，以便轻松跟踪部署。

## <a name="update-an-azure-database-for-mariadb-server"></a>更新 Azure Database for MariaDB 服务器
预配新服务器后, 用户有多个选项可用于配置现有服务器, 包括重置管理员密码、更改定价层以及通过更改 vCore 或存储来向上或向下扩展服务器。

### <a name="change-the-administrator-user-password"></a>更改管理员用户密码
1. 从服务器“概览”中，单击“重置密码”以显示密码重置窗口。

   ![概述](./media/howto-create-manage-server-portal/overview.png)

2. 在窗口中输入并确认新密码，如下所示：

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. 单击“确定”以保存新密码。

### <a name="change-the-pricing-tier"></a>更改定价层
> [!NOTE]
> 仅支持对内存优化常规用途的服务层进行缩放, 反之亦然。 请注意, 在 Azure Database for MariaDB 中不支持在创建服务器后从基本定价层更改为和。
> 
1. 单击“设置”下的“定价层”。
2. 选择要更改为的**定价层**。

    ![更改-定价层](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. 单击“确定”保存更改。 

### <a name="scale-vcores-updown"></a>纵向扩展/收缩 vCore

1. 单击“设置”下的“定价层”。

2. 通过将滑块移动到所需的值来更改“vCore”设置。

    ![scale-compute](./media/howto-create-manage-server-portal/scale-compute.png)

3. 单击“确定”保存更改。

### <a name="scale-storage-up"></a>纵向扩展存储

1. 单击“设置”下的“定价层”。

2. 通过将滑块移动到所需的值来更改“存储”设置。

    ![scale-storage](./media/howto-create-manage-server-portal/scale-storage.png)

3. 单击“确定”保存更改。

## <a name="delete-an-azure-database-for-mariadb-server"></a>删除 Azure Database for MariaDB 服务器

1. 在服务器“概览”中，单击“删除”命令按钮以打开删除确认提示。

    ![删除](./media/howto-create-manage-server-portal/delete.png)

2. 在输入框中键入服务器名称以再次确认。

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. 单击“删除”按钮以确认删除服务器。 等待通知栏中显示 "已成功删除 MariaDB 服务器" 弹出窗口。

## <a name="list-the-azure-database-for-mariadb-databases"></a>列出 Azure Database for MariaDB 数据库
在服务器“概览”中，向下滚动，直到看到底部的数据库磁贴。 服务器中的所有数据库都在表中列出。

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mariadb-server"></a>显示 Azure Database for MariaDB 服务器的详细信息
单击“设置”下的“属性”以查看有关服务器的详细信息。

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>后续步骤

[快速入门：使用 Azure 门户创建 Azure Database for MariaDB 服务器](./quickstart-create-mariadb-server-database-using-azure-portal.md)