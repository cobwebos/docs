---
title: "使用 Azure 门户创建和管理 Azure Database for MySQL 服务器 | Microsoft Docs"
description: "本文介绍了如何使用 Azure 门户快速创建一个新的 Azure Database for MySQL 服务器，以及如何使用 Azure 门户管理该服务器。"
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: cbf1bcd90c53a7221773c7dc7731b37cf762e953
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>使用 Azure 门户创建和管理 Azure Database for MySQL 服务器
本文介绍了如何使用 Azure 门户快速创建一个新的 Azure Database for MySQL 服务器，以及如何使用 Azure 门户管理该服务器。 服务器管理包括查看服务器的详细信息和数据库、重置密码以及删除服务器。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户
登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器
请按照下列步骤，创建一个名为“mysqlserver4demo”的 Azure Database for MySQL 服务器

1- 单击 Azure 门户左上角的“新建”按钮。

2- 从“新建”页中选择“数据库”，然后从“数据库”页中选择“Azure Database for MySQL”。

> 创建的 Azure Database for MySQL 服务器中包含一组已定义的[计算和存储](./concepts-compute-unit-and-storage.md)资源。 创建的数据库位于 Azure Database for MySQL 服务器的 Azure 资源组中。

![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3- 使用以下信息填写 Azure Database for MySQL 窗体：

| 窗体字段 | 字段说明 |
|----------------|-----------------------|
| *服务器名称* | azure-mysql（服务器名称是全局唯一的） |
| *订阅* | MySQLaaS（从下拉列表中选择） |
| *资源组* | myresource（新建一个资源组或使用现有资源组） |
| 服务器管理员登录名 | myadmin（设置管理员帐户名称） |
| *密码* | 设置管理员帐户密码 |
| *确认密码* | 确认管理员帐户密码 |
| *位置* | 北欧（选择北欧或美国西部） |
| *版本* | 5.6（选择 Azure Database for MySQL 服务器版本） |

4- 单击“定价层”，为新服务器指定服务层和性能级别。 在基本层中可配置 50 到 100 个计算单元，在标准层中可以配置 100 到 200 个，可以根据包含的数量来添加存储空间。 在本操作方法指南中，选择了 50 个计算单元和 50 GB 空间。 单击“确定”保存选择内容。
![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5- 单击“创建”预配服务器。 预配需要数分钟。

> 选中“固定到仪表板”选项，轻松跟踪部署。
> [!NOTE]
> 虽然在基本层和标准层中支持的存储空间分别高达 1000GB 和 10000GB，但对于公共预览版，最大存储空间暂时仍限制为 1000GB。 
</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>更新 Azure Database for MySQL 服务器
预配新服务器后，用户可通过两个选项来编辑现有服务器：重置管理员密码或通过更改计算单元来增加/减少服务器。

### <a name="change-the-administrator-user-password"></a>更改管理员用户密码
1- 在服务器的“概述”边栏选项卡中，单击“重置密码”，填充密码输入和密码确认窗口。

2- 在窗口中输入并确认新密码，如下图所示：![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3- 单击“确定”保存新密码。

### <a name="scale-updown-by-changing-compute-units"></a>通过更改计算单元增加/减少

1- 在服务器边栏选项卡上的“设置”下，单击“定价层”，打开 Azure Database for MySQL 服务器的定价层边栏选项卡。

2- 按照**创建 Azure Database for MySQL 服务器**中的步骤 4 进行操作，更改同一定价层中的计算单元。

## <a name="delete-an-azure-database-for-mysql-server"></a>删除 Azure Database for MySQL 服务器

1- 在服务器的“概述”边栏选项卡上，单击“删除”命令按钮，打开删除确认边栏选项卡。

2- 在边栏选项卡的输入框中，键入正确的服务器名称，再次进行确认。

3- 再次单击“删除”按钮确认删除操作，等待通知栏上弹出“删除成功”窗口。

## <a name="list-the-azure-database-for-mysql-databases"></a>列出 Azure Database for MySQL 数据库
在服务器的“概述”边栏选项卡上向下滚动，直到底部出现数据库磁贴。 所有数据库都将在表中列出。 单击“删除”命令按钮，打开删除确认边栏选项卡。

![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>显示 Azure Database for MySQL 服务器的详细信息
在服务器边栏选项卡上的“设置”下，单击“属性”可以打开“属性”边栏选项卡。 然后可以查看有关服务器的详细信息。

## <a name="next-steps"></a>后续步骤

[快速入门：使用 Azure 门户创建 Azure Database for MySQL 服务器](./quickstart-create-mysql-server-database-using-azure-portal.md)

