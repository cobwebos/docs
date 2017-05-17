---
title: "在 Azure Database for MySQL 中进行导入和导出 | Microsoft Docs"
description: "介绍在 Azure Database for MySQL 中导入和导出数据库"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 582f83e3e80d855049abece42726748fcd113ab7
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-using-import-and-export"></a>使用导入和导出迁移 MySQL 数据库
本教程将演示使用 MySQL Workbench 在 Azure MySQL 数据库中导入和导出数据的常用方法。 

## <a name="before-you-begin"></a>开始之前
若要逐步执行本操作方法指南，需要：
- Azure Database for MySQL 服务器 [设计你的第一个 Azure MySQL 数据库](quickstart-create-mysql-server-database-using-azure-portal.md)
- MySQL Workbench [MySQL Workbench下载](https://dev.mysql.com/downloads/workbench/)

## <a name="use-common-tools"></a>使用常用工具
使用常用工具（例如 MySQL Workbench、Toad 或 Navicat）进行远程连接，并将数据导入或导出到 Azure Database for MySQL。 在具有 Internet 连接的客户端计算机上使用此类工具连接到 Azure Database for MySQL。 使用具有 SSL 加密的连接是最安全的做法，另请参阅[在 Azure Database for MySQL 中配置 SSL 连接](concepts-ssl-connection-security.md)。 迁移到 Azure Database for MySQL 时，无需将导入和导出文件移到任何特殊的云位置。 

## <a name="create-a-database-on-the-target-azure-database-for-mysql-service"></a>在目标 Azure Database for MySQL 服务上创建数据库
必须在目标 Azure Database for MySQL 服务器（要在其中使用面向 MySQL 的 MySQL Workbench、Toad、Navicat 或任何第三方工具迁移数据）上创建一个空数据库。 数据库名称可与包含转储数据的数据库名称相同，或可以创建一个不同名称的数据库。

![Azure Database for MySQL 连接字符串](./media/concepts-migrate-import-export/p5.png)

![MySQL Workbench 连接字符串](./media/concepts-migrate-import-export/p4.png)

## <a name="import-and-export-using-mysql-workbench"></a>使用 MySQL Workbench 进行导入和导出
有两种方法在 MySQL Workbench 中导入和导出数据，每种方法的用途不同。 

## <a name="table-data-export-and-import-wizard-using-object-browser-context-menu"></a>使用“对象浏览器”上下文菜单的表数据导出和导入向导
![使用“对象浏览器”上下文菜单的 MySQL Workbench 导入/导出](./media/concepts-migrate-import-export/p1.png)

此向导支持使用 CSV 和 JSON 文件的导入和导出操作，且包括多个配置选项（分隔符、列选择、编码选择等）。 可以针对本地或远程连接的 MySQL 服务器执行此向导，导入操作包括表、列和类型映射。 右键单击表，从“对象浏览器”的上下文菜单中访问此向导，并选择“表数据导出向导”或“表数据导入向导”。 

## <a name="table-data-export-wizard"></a>表数据导出向导
下面的示例将表导出到 CSV 文件。 
- 右键单击要导出的数据库的表。 
- 选择“表数据导出向导”。 选择要导出的列、行偏移量（如果存在）、计数（如果存在）。 
- 在“选择要导出的数据”窗口中，单击“下一步”。 选择文件路径、CSV 或 JSON 文件类型、行分隔符、包围字符串和字段分隔符。 
- 在“选择输出文件位置”窗口中，选择“下一步”，并在“导出数据”窗口中选择“下一步”。


## <a name="table-data-import-wizard"></a>表数据导入向导
下面的示例将从 CSV 文件导入表。
- 右键单击要导入的数据库的表。 
- 浏览并选择要导入的 CSV 文件，然后单击“下一步”按钮。 
- 选择目标表（新的或现有的），然后选择或取消选择“导入前截断表”复选框并单击“下一步”按钮。
- 选择编码和要导入的列，然后选择“下一步”按钮。 
- 在“导入数据”窗口中选择“下一步”将相应地导入数据。

## <a name="sql-data-export-and-import-wizard-from-management-navigator"></a>管理导航器中的 SQL 数据导出和导入向导
使用此向导以导出或导入 MySQL Workbench 中生成的或使用 mysqldump 命令生成的 SQL。 从导航器面板访问这些向导，或通过选择主菜单中的服务器，然后选择“数据导入”或“数据导出”进行访问。 

## <a name="data-export"></a>数据导出
![使用管理导航器的 MySQL Workbench 数据导出](./media/concepts-migrate-import-export/p2.png)

使用此选项卡能够导出 MySQL 数据。 
- 选择想导出的每个架构，（可选）从每个架构中选择特定的架构对象/表，并生成导出。 配置选项包括导出到项目文件夹或自包含的 SQL 文件、（可选）转储存储的例程和事件（可选），或跳过表数据。 
- 或者，使用“导出结果集”将 SQL 编辑器中的特定结果集导出为其他格式，例如 CSV、JSON、HTML 和 XML。 
- 选择要导出的数据库对象，并配置相关选项。 
- 单击“刷新”加载当前对象。 
- （可选）打开可以完善导出操作的“高级选项”选项卡。 例如，添加表锁、使用替换而不是 insert 语句、使用反撇号字符将标识符括起来等。 
- 单击“开始导出”启动导出过程。 


## <a name="data-import"></a>数据导入
![使用管理导航器的 MySQL Workbench 数据导入](./media/concepts-migrate-import-export/p3.png)

使用此选项卡能够从“数据导出”操作或从 mysqldump 命令中其他导出的数据导入或还原导出的数据。 
- 选择项目文件夹或自包含的 SQL 文件，选择要向其中导入数据的架构，或选择“新建”以定义新的架构。 
- 单击“开始导入”启动导入过程，它将相应地执行导入。

## <a name="next-steps"></a>后续步骤
如果不熟悉如何开始使用此数据库服务，请参阅：
-  [使用 Azure 门户创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md) 
- [使用 Azure CLI 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)

