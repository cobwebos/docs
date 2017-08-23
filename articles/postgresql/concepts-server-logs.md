---
title: "Azure Database for PostgreSQL 中的服务器日志 | Microsoft Docs"
description: "在 Azure Database for PostgreSQL 中生成查询和错误日志。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c384171c806e929a425e4299c81f0fb2321dc91b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中的服务器日志 
Azure Database for PostgreSQL 生成查询和错误日志。 但不支持访问事务日志。 这些日志可用于识别、排除和修复配置错误和性能不佳的问题。 有关详细信息，请参阅[错误报告和日志记录](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html)。

## <a name="access-server-logs"></a>访问服务器日志
可以使用 Azure 门户、[Azure CLI](howto-configure-server-logs-using-cli.md) 和 Azure REST API 列出和下载 Azure PostgreSQL 服务器错误日志。

## <a name="log-retention"></a>日志保留期
可以使用与服务器关联的 log\_retention\_ 参数设置系统日志的保留期。 此参数的单位为天（需确认）。 默认值为 3 天。 最大值为 7 天。 请注意，必须为服务器分配足够的存储空间，以便存储保留的日志文件。
每 1 小时或达到 100MB 大小时日志文件会轮换一次，以先达到的限制为准。

## <a name="configure-logging-for-azure-postgresql-server"></a>配置 Azure PostgreSQL 服务器的日志
可以启用服务器的查询日志和错误日志。 错误日志包含自动清空、连接和检查点等信息。

可以通过设置以下两个参数为 PostgreSQL DB 实例启用查询日志记录：log\_statement 和 log\_min\_duration\_statement。

**log\_statement** 参数控制要记录的 SQL 语句。 建议将此参数设置为“所有”以便记录所有语句；默认值为“无”（需确认）。

log\_min\_duration\_statement 参数可以设置记录语句时的时间限制（以毫秒为单位）。 运行时间超过该参数设置的所有 SQL 语句将被记录下来。 默认情况下禁用此参数并将其设置为负 1 (-1)（需确认）。 启用此参数有助于跟踪应用程序中效果不佳的查询。

通过 log\_min\_messages 可控制写入服务器日志的消息级别。 默认设置为“警告”。 （需确认）

有关这些设置的详细信息，请参阅[错误报告和日志记录](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html)文档。 有关配置 Azure Database for PostgreSQL 服务器参数的详细信息，请参阅 [Azure Database for PostgreSQL 中的服务器日志](concepts-server-logs.md)。

## <a name="next-steps"></a>后续步骤
- 若要使用 Azure CLI 命令行接口访问日志，请参阅[使用 Azure CLI 配置和访问服务器日志](howto-configure-server-logs-using-cli.md)
- 有关服务器参数的详细信息，请参阅[使用 Azure CLI 自定义服务器配置参数](howto-configure-server-parameters-using-cli.md)。
