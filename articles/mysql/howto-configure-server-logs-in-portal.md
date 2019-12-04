---
title: 访问慢查询日志-Azure 门户-Azure Database for MySQL
description: 本文介绍如何在 Azure 门户中配置和访问 Azure Database for MySQL 中的慢速日志。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 52d219a94676e0f1ce4ceac31b0b50c9d36665ba
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770571"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>配置和访问 Azure 门户的慢速查询日志

可以从 Azure 门户配置、列出和下载[Azure Database for MySQL 慢速查询日志](concepts-server-logs.md)。

## <a name="prerequisites"></a>必备组件
本文中的步骤要求您具有[Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)。

## <a name="configure-logging"></a>配置日志记录
配置 MySQL 慢查询日志的访问权限。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择 Azure Database for MySQL 服务器。

3. 在边栏中的 "**监视**" 部分下，选择 "**服务器日志**"。 
   服务器日志选项 ![屏幕截图](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. 若要查看服务器参数，请选择 "**单击此处以启用日志并配置日志参数**"。

5. 更改需要调整的参数。 在此会话中所做的更改都突出显示为紫色。 

   更改参数后，请选择 "**保存**"。 也可以放弃所做的更改。

   ![服务器参数选项的屏幕截图](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

在 "**服务器参数**" 页中，可以通过关闭页面返回到日志列表。

## <a name="view-list-and-download-logs"></a>查看列表并下载日志
日志记录开始后，你可以查看可用的慢速查询日志列表，并下载单独的日志文件。

1. 打开 Azure 门户。

2. 选择 Azure Database for MySQL 服务器。

3. 在边栏中的 "**监视**" 部分下，选择 "**服务器日志**"。 页面将显示日志文件的列表。

   !["服务器日志" 页的屏幕截图，其中突出显示了日志列表](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > 日志命名约定是 mysql-slow-< your server name>-yyyymmddhh.log。 文件名中使用的日期和时间是发出日志的时间。 日志文件每24小时或 7.5 GB 轮换一次，以先达到的频率为准。 

4. 如果需要，可以使用 "搜索" 框根据日期和时间快速缩小到特定日志。 搜索按日志名称进行。

5. 若要下载单独的日志文件，请选择表行中每个日志文件旁边的向下箭头图标。

   !["服务器日志" 页的屏幕截图，突出显示了向下箭头图标](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

1. 在边栏中的 "**监视**" 部分下，选择 "**诊断设置**" > "**添加诊断设置**"。

   ![诊断设置选项的屏幕截图](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. 提供诊断设置名称。

1. 指定发送慢查询日志的数据接收器（存储帐户、事件中心或 Log Analytics 工作区）。

1. 选择**MySqlSlowLogs**作为日志类型。
诊断设置配置选项的 ![屏幕截图](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. 配置数据接收器以通过管道将慢速查询日志传输到之后，请选择 "**保存**"。
诊断设置配置选项的 ![屏幕截图，突出显示保存](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. 通过在配置的数据接收器中浏览这些日志来访问慢速查询日志。 可能需要长达10分钟的时间才会显示日志。

## <a name="next-steps"></a>后续步骤
- 有关如何以编程方式下载缓慢查询日志的详细说明，请参阅[在 CLI 中访问慢查询日志](howto-configure-server-logs-in-cli.md)。
- 详细了解 Azure Database for MySQL 中的[慢速查询日志](concepts-server-logs.md)。
- 有关参数定义和 MySQL 日志记录的详细信息，请参阅有关[日志](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)的 mysql 文档。