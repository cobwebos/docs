---
title: 使用 Azure 数据资源管理器 ODBC 连接器直观显示 Tableau 数据
description: 本文介绍如何使用开放式数据库连接（ODBC）连接到 Azure 数据资源管理器连接，通过 Tableau 实现数据的可视化。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562439"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>可视化 Tableau 中 Azure 数据资源管理器的数据

 [Tableau](https://www.tableau.com/)是用于商业智能的可视化分析平台。 若要从 Tableau 连接到 Azure 数据资源管理器并从示例群集引入数据，请使用 SQL Server 开放式数据库连接（ODBC）驱动程序。 

## <a name="prerequisites"></a>必备条件

若要完成本文，你需要以下各项：

* 使用 SQL Server ODBC 驱动程序连接到[azure 数据资源管理器](connect-odbc.md)，通过 Tableau 连接到 azure 数据资源管理器。 

* Tableau Desktop、full 或[试用](https://www.tableau.com/products/desktop/download)版。

* 包括 StormEvents 示例数据的群集。 有关详细信息，请参阅[创建 azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)，并将[示例数据引入 azure 数据资源管理器](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>可视化 Tableau 中的数据 

完成 ODBC 配置后，即可将示例数据引入 Tableau。

1. 在 Tableau Desktop 中的左侧菜单中，选择 "**其他数据库（ODBC）** "。

    ![与 ODBC 连接](media/tableau/connect-odbc.png)

1. 对于**DSN**，选择你为 ODBC 创建的数据源，然后选择 "**登录**"。

    ![ODBC 登录](media/tableau/odbc-sign-in.png)

1. 对于 "**数据库**"，请选择示例群集上的数据库，例如*TestDatabase*。 对于 "**架构**"，选择 " *dbo*"，对于 "**表**"，请选择*StormEvents*示例表。

    ![选择数据库和表](media/tableau/select-database-table.png)

1. Tableau 现在显示示例数据的架构。 选择 "**立即更新**"，将数据导入 Tableau。

    ![更新数据](media/tableau/update-data.png)

    导入数据时，Tableau 会显示类似于下图的数据行。

    ![结果集](media/tableau/result-set.png)

1. 现在，可以根据从 Azure 数据资源管理器中引入的数据在 Tableau 中创建可视化效果。 有关详细信息，请参阅[Tableau Learning](https://www.tableau.com/learn)。

## <a name="next-steps"></a>后续步骤

* [Azure 数据资源管理器的编写查询](write-queries.md)