---
title: 通过 SQL Server Management Studio 使用 Azure Blockchain Workbench 数据
description: 了解如何从 SQL Server Management Studio 中连接到 Azure Blockchain Workbench 的 SQL 数据库。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 5c5a2e2b42cd9c19810e6579159b228369ee8f7f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969293"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>通过 SQL Server Management Studio 使用 Azure Blockchain Workbench 数据

利用 Microsoft SQL Server Management Studio，可以快速编写和测试针对 Azure Blockhain Workbench 的 SQL DB 的查询。 本部分包含有关如何从 SQL Server Management Studio 中连接到 Azure Blockchain Workbench 的 SQL 数据库的分步演练。

## <a name="prerequisites"></a>先决条件

* 下载 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)。

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>将 SQL Server Management Studio 连接到 Azure Blockchain Workbench 中的数据

1. 打开 SQL Server Management Studio 并选择“连接”。
2. 选择“数据库引擎”。

    ![数据库引擎](media/blockchain-workbench-data-sql-management-studio/database-engine.png)

3. 在“连接到服务器”对话框中，输入服务器名称和数据库凭据。

    如果使用由 Azure Blockchain Workbench 部署过程创建的凭据，则用户名将是 **dbadmin** 并且密码将是在部署过程中提供的密码。

    ![输入 SQL 凭据](media/blockchain-workbench-data-sql-management-studio/sql-creds.png)

 4. SQL Server Management Studio 显示 Azure Blockchain Workbench 数据库中的数据库、数据库视图和存储过程的列表。

    ![数据库列表](media/blockchain-workbench-data-sql-management-studio/db-list.png)

5. 若要查看与任何数据库视图关联的数据，可以使用以下步骤自动生成 select 语句。
6. 右键单击对象资源管理器中的任何数据库视图。
7. 选择“编写视图脚本为”。
8. 选择“SELECT to”。
9. 选择“新建查询编辑器窗口”。
10. 可以通过选择“新建查询”来创建新查询。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 中的数据库视图](blockchain-workbench-database-views.md)