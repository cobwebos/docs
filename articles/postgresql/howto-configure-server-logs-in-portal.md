---
title: 管理日志-Azure 门户-Azure Database for PostgreSQL-单一服务器
description: 本文介绍如何在 Azure 门户中配置和访问 Azure Database for PostgreSQL 单服务器中的服务器日志（.log 文件）。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763680"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>配置并访问 Azure 门户中的 Azure Database for PostgreSQL 单服务器日志

可以从 Azure 门户配置、列出和下载[Azure Database for PostgreSQL 日志](concepts-server-logs.md)。

## <a name="prerequisites"></a>必备组件
本文中的步骤要求您具有[Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)。

## <a name="configure-logging"></a>配置日志记录
配置对查询日志和错误日志的访问权限。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在边栏中的 "**监视**" 部分下，选择 "**服务器日志**"。 

   ![服务器日志选项的屏幕截图](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. 若要查看服务器参数，请选择 "**单击此处以启用日志并配置日志参数**"。

5. 更改需要调整的参数。 在此会话中所做的更改都突出显示为紫色。

   更改参数后，请选择 "**保存**"。 也可以放弃所做的更改。 

   ![服务器参数选项的屏幕截图](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

在 "**服务器参数**" 页中，可以通过关闭页面返回到日志列表。

## <a name="view-list-and-download-logs"></a>查看列表并下载日志
日志记录开始后，你可以查看可用日志列表，并下载单个日志文件。 

1. 打开 Azure 门户。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在边栏中的 "**监视**" 部分下，选择 "**服务器日志**"。 页面将显示日志文件的列表。

   !["服务器日志" 页的屏幕截图，其中突出显示了日志列表](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > 日志的命名约定是 **postgresql-yyyy-mm-dd_hh0000.log**。 文件名中使用的日期和时间是发出日志的时间。 日志文件每小时或每 100 MB 旋转一次，以先达到的频率为准。

4. 如果需要，可以使用 "搜索" 框根据日期和时间快速缩小到特定日志。 搜索按日志名称进行。

   !["服务器日志" 页的屏幕截图，其中突出显示了 "搜索框和结果"](./media/howto-configure-server-logs-in-portal/5-search.png)

5. 若要下载单独的日志文件，请选择表行中每个日志文件旁边的向下箭头图标。

   !["服务器日志" 页的屏幕截图，突出显示了向下箭头图标](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>后续步骤
- 请参阅[在 CLI 中访问服务器日志](howto-configure-server-logs-using-cli.md)以了解如何以编程方式下载日志。
- 详细了解 Azure Database for PostgreSQL 中的[服务器日志](concepts-server-logs.md)。 
- 有关参数定义和 PostgreSQL 日志记录的详细信息，请参阅[错误报告和日志记录](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)上的 PostgreSQL 文档。

