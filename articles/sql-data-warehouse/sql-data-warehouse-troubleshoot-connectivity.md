---
title: 排查 Azure SQL 数据仓库问题 | Microsoft Docs
description: 排查 Azure SQL 数据仓库问题。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 03/27/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 5115ffbc3568c87c37bae4a3e65c37f8504f1fb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475273"
---
# <a name="troubleshooting-connectivity-issues"></a>排查连接问题

本文列出了有关连接到 SQL 数据仓库的常见故障排除技术。
- [检查服务可用性](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [检查已暂停或缩放操作](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [检查防火墙设置](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [检查你的 VNet/服务终结点设置](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [检查最新驱动程序](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [检查你的连接字符串](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [出现间歇性连接问题](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [常见错误消息](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>检查服务可用性

检查服务是否可用。 在 Azure 门户中，转到想要连接的 SQL 数据仓库。 在左侧的目录窗格中，单击**诊断并解决问题**。

![选择资源运行状况](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

SQL 数据仓库的状态将如下所示。 如果该服务未显示作为**可用**，进一步检查步骤。

![可用的服务](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

如果资源运行状况显示数据仓库已暂停或缩放，按照恢复数据仓库的指南。

![服务暂停](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)有关资源运行状况的其他信息可在此处找到。

## <a name="check-for-paused-or-scaling-operation"></a>检查已暂停或缩放操作

请检查门户才能看到是否 SQL 数据仓库已暂停或缩放。

![暂停服务](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

如果你看到你的服务已暂停或缩放，请检查期间维护日程安排不是。 在门户中为 SQL 数据仓库上*概述*，您将看到所选的维护计划。

![概述维护日程安排](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

否则，请与 IT 管理员联系以验证此维护并不预定的事件。 若要恢复的 SQL 数据仓库，请按照所述的步骤[此处](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)。

## <a name="check-your-firewall-settings"></a>检查防火墙设置

SQL 数据仓库通过端口 1433 进行通信。   如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 在这种情况下，您无法连接到 Azure SQL 数据库服务器除非 IT 部门打开了端口 1433年。 找不到防火墙配置的其他信息[此处](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json#manage-ip-firewall-rules-using-the-azure-portal)。

## <a name="check-your-vnetservice-endpoint-settings"></a>检查你的 VNet/服务终结点设置

如果你收到错误 40914 和 40615，请参阅[错误说明和解决此处](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)。

## <a name="check-for-the-latest-drivers"></a>检查最新驱动程序

### <a name="software"></a>软件

检查以确保您要使用最新的工具连接到 SQL 数据仓库：

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>驱动程序

检查以确保使用最新的驱动程序版本。  使用较旧版本的驱动程序可能导致意外的行为，如旧驱动程序可能不支持新功能。

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>检查你的连接字符串

检查以确保正确设置连接字符串。  下面是一些示例。  您可以找到更多相关信息[连接字符串此处](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings)。

ADO.NET 连接字符串

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC 连接字符串

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP 连接字符串

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC 连接字符串

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>出现间歇性连接问题

检查以确定是否遇到具有大量的排队请求的服务器负载过重的情况。 您可能需要纵向扩展数据仓库的其他资源。

## <a name="common-error-messages"></a>常见错误消息

错误 40914 和 40615，请参阅[错误说明和解决此处](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)。

## <a name="still-having-connectivity-issues"></a>仍然遇到连接问题？
创建[支持票证](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket)因此工程团队可以支持您。
