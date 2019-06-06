---
title: 使用 ODBC 连接到 Azure 数据资源管理器
description: 在本文中，您将了解如何设置开放式数据库连接 (ODBC) 连接到 Azure 数据资源管理器。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 02ae9673f1dc402ee1500b466d7e259263ef3262
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494850"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>使用 ODBC 连接到 Azure 数据资源管理器

开放式数据库连接 ([ODBC](/sql/odbc/reference/odbc-overview)) 是一种广泛接受的应用程序编程接口 (API)，适用于数据库访问。 使用 ODBC 可从没有专用连接器的应用程序连接到 Azure 数据资源管理器。

在后台，应用程序会在 ODBC 接口中调用函数，这些函数在特定于数据库的模块（称为“驱动程序”）中实现。  Azure 数据资源管理器支持 SQL Server 通信协议的一个子集 ([MS TDS](/azure/kusto/api/tds/))，因此它可以使用 SQL Server 的 ODBC 驱动程序。

本文介绍如何使用 SQL Server ODBC 驱动程序，目的是让你能够从任何支持 ODBC 的应用程序连接到 Azure 数据资源管理器。

## <a name="prerequisites"></a>必备组件

需要满足以下条件：

* 适用于操作系统的 [Microsoft ODBC Driver for SQL Server 17.2.0.1 或更高版本](/sql/connect/odbc/download-odbc-driver-for-sql-server)。

## <a name="configure-the-odbc-data-source"></a>配置 ODBC 数据源

按以下步骤操作，使用 ODBC Driver for SQL Server 配置 ODBC 数据源。

1. 在 Windows 中搜索“ODBC 数据源”，  打开 ODBC 数据源桌面应用。

1. 选择 **添加** 。

    ![添加数据源](media/connect-odbc/add-data-source.png)

1. 选择“ODBC Driver 17 for SQL Server”，然后选择“完成”。  

    ![选择驱动程序](media/connect-odbc/select-driver.png)

1. 输入连接的名称和说明以及要连接到的群集，然后选择“下一步”  。 群集 URL 应该采用 *\<ClusterName\>.\<区域\>.kusto.windows.net* 格式。

    ![选择服务器](media/connect-odbc/select-server.png)

1. 选择“Active Directory 集成”，然后选择“下一步”。  

    ![Active Directory 集成](media/connect-odbc/active-directory-integrated.png)

1. 选择包含示例数据的数据库，然后选择“下一步”。 

    ![更改默认数据库](media/connect-odbc/change-default-database.png)

1. 在下一屏幕中将所有选项保留为默认设置，然后选择“完成”。 

1. 选择“测试数据源”  。

    ![测试数据源](media/connect-odbc/test-data-source.png)

1. 验证测试是否成功，然后选择“确定”。  如果测试不成功，请检查在前面的步骤中指定的值，并确保有足够的权限连接到群集。

    ![测试成功](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>后续步骤

* [Tableau 从连接到 Azure 的数据资源管理器](tableau.md)