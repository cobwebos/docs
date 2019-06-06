---
title: 使用开放式数据库连接 (ODBC) 连接到 Azure 数据资源管理器来使用 Tableau 直观显示数据
description: 在本文中，您将了解如何使用 Azure 数据资源管理器连接到开放式数据库连接 (ODBC) 连接进行使用 Tableau 直观显示数据。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499079"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>从 Azure 数据资源管理器在 Tableau 中可视化数据

 [Tableau](https://www.tableau.com/)是商业智能可视化分析平台。 若要连接到 Azure 数据资源管理器从 Tableau 和示例群集中引入的数据，使用 SQL Server 开放式数据库连接 (ODBC) 驱动程序。 

## <a name="prerequisites"></a>必备组件

若要完成本文，您需要：

* [连接到 Azure 数据资源管理器使用 ODBC](connect-odbc.md)使用 SQL Server ODBC 驱动程序，从 Tableau 连接到 Azure 数据资源管理器。 

* Tableau Desktop 中，完整，或[试用](https://www.tableau.com/products/desktop/download)版本。

* 包括 StormEvents 示例数据的群集。 有关详细信息，请参阅[创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)并[将示例数据引入到 Azure 数据资源管理器](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Tableau 将数据可视化 

一旦完成配置 ODBC 后，您可以将示例数据引入 Tableau。

1. 在 Tableau Desktop 的左侧菜单中，选择“其他数据库(ODBC)”。 

    ![与 ODBC 连接](media/tableau/connect-odbc.png)

1. 至于“DSN”，请选择为 ODBC 创建的数据源，然后选择“登录”。  

    ![ODBC 登录](media/tableau/odbc-sign-in.png)

1. 至于“数据库”，请选择在示例群集上创建的数据库，例如“TestDatabase”。   至于“架构”，请选择“dbo”；至于“表”，请选择“StormEvents”示例表。    

    ![选择数据库和表](media/tableau/select-database-table.png)

1. Tableau 现在会显示示例数据的架构。 选择“立即更新”，将数据引入 Tableau。 

    ![更新数据](media/tableau/update-data.png)

    将数据引入以后，Tableau 会显示类似于下图的数行数据。

    ![结果集](media/tableau/result-set.png)

1. 现在可以根据你从 Azure 数据资源管理器引入的数据在 Tableau 中创建可视化效果。 有关详细信息，请参阅 [Tableau 学习](https://www.tableau.com/learn)。

## <a name="next-steps"></a>后续步骤

* [Azure 数据资源管理器的编写查询](write-queries.md)