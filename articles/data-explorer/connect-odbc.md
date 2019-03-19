---
title: 连接到 Azure 数据资源管理器中使用 ODBC
description: 在本操作指南，您将学习如何设置 ODBC 连接到 Azure 数据资源管理器，然后使用该连接以使用 Tableau 直观显示数据。
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: ad00ad247b047d4acf97eb5d0e96229949181ecf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740173"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>连接到 Azure 数据资源管理器中使用 ODBC

开放式数据库连接 ([ODBC](/sql/odbc/reference/odbc-overview)) 是数据库访问权限被广泛接受的应用程序编程接口 (API)。 使用 ODBC 连接到 Azure 数据资源管理器从没有专用的连接器的应用程序。

在后台，应用程序调用 ODBC 接口，这在调用特定于数据库的模块中实现的函数*驱动程序*。 Azure 数据资源管理器支持 SQL Server 通信协议的一个子集 ([MS TDS](/azure/kusto/api/tds/)); 因此它可用于 SQL Server 的 ODBC 驱动程序。

在本文中，您将了解如何使用 SQL Server ODBC 驱动程序，因此你可以从支持 ODBC 的任何应用程序连接到 Azure 数据资源管理器。 你可以然后 （可选） 从 Tableau，连接到 Azure 数据资源管理器并从示例群集中的数据引入。

## <a name="prerequisites"></a>必备组件

需要以下各项来完成这一过程：

* [Microsoft ODBC Driver for SQL Server 版本 17.2.0.1 或更高版本](/sql/connect/odbc/download-odbc-driver-for-sql-server)适用于操作系统。

* 如果你想要按照我们 Tableau 的示例中，您还需要：

  * Tableau Desktop 中，完整或[试用](https://www.tableau.com/products/desktop/download)版本。

  * 包括 StormEvents 示例数据的群集。 有关详细信息，请参阅[快速入门：创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)和[将示例数据引入到 Azure 数据资源管理器](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>配置 ODBC 数据源

请按照以下步骤来配置 ODBC 数据源使用的 SQL Server ODBC 驱动程序。

1. 在 Windows 中，搜索*ODBC 数据源*，并打开 ODBC 数据源桌面应用程序。

1. 选择 **添加** 。

    ![添加数据源](media/connect-odbc/add-data-source.png)

1. 选择**ODBC Driver 17 for SQL Server**然后**完成**。

    ![选择驱动程序](media/connect-odbc/select-driver.png)

1. 输入名称和描述，连接，并且想要连接到的群集然后选择**下一步**。 群集 URL 应采用格式 *\<ClusterName\>。\<区域\>。 kusto.windows.net*。

    ![选择服务器](media/connect-odbc/select-server.png)

1. 选择**集成了 Active Directory**然后**下一步**。

    ![Active Directory 集成](media/connect-odbc/active-directory-integrated.png)

1. 然后选择使用示例数据的数据库**下一步**。

    ![更改默认数据库](media/connect-odbc/change-default-database.png)

1. 在下一个屏幕中，保留所有选项为默认值然后选择**完成**。

1. 选择**测试数据源**。

    ![测试数据源](media/connect-odbc/test-data-source.png)

1. 验证测试是否成功，然后选择**确定**。 如果测试失败，请检查你在上一步骤中指定的值，并确保您具有足够权限来连接到群集。

    ![成功进行测试](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Tableau （可选） 将数据可视化

现在已完成的配置 ODBC，可以将示例数据导入 Tableau。

1. 在 Tableau Desktop 中，在左侧菜单中，选择**其他数据库 (ODBC)**。

    ![与 ODBC 连接](media/connect-odbc/connect-odbc.png)

1. 有关**DSN**，选择用于 ODBC，创建的数据源，然后选择**登录**。

    ![ODBC 登录](media/connect-odbc/odbc-sign-in.png)

1. 有关**数据库**，选择数据库上的示例群集，如*TestDatabase*。 有关**架构**，选择*dbo*，并为**表**，选择*StormEvents*示例表。

    ![选择数据库和表](media/connect-odbc/select-database-table.png)

1. Tableau 现在显示示例数据的架构。 选择**立即更新**若要将数据导入 Tableau。

    ![更新数据](media/connect-odbc/update-data.png)

    导入数据，则 Tableau 将显示类似于下图的数据行。

    ![结果集](media/connect-odbc/result-set.png)

1. 现在可以基于从 Azure 数据资源管理器引入的数据在 Tableau 中创建可视化效果。 有关详细信息，请参阅[Tableau 学习](https://www.tableau.com/learn)。

## <a name="next-steps"></a>后续步骤

[Azure 数据资源管理器的编写查询](write-queries.md)

[教程：在 Power BI 中可视化 Azure 数据资源管理器中的数据](visualize-power-bi.md)