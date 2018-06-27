---
title: 将数据复制到 Azure Database for MySQL。
description: 本文介绍 Azure Database for MySQL 的复制中数据。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: 72f8211ecc0534b15402911de8fc0ec3d541a835
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294898"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>将数据复制到 Azure Database for MySQL

借助数据复制，可以将本地运行的 MySQL 服务器、虚拟机或其他云提供程序托管的数据库服务中的数据同步到 Azure Database for MySQL 服务。 复制中数据以基于二进制日志 (binlog) 文件位置的从本机到 MySQL 的复制为基础。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。 

## <a name="when-to-use-data-in-replication"></a>何时使用配置复制中数据
可以考虑使用复制中数据的主要场景有：

- 混合数据同步：借助复制中数据，可以在本地服务器和 Azure Database for MySQL 之间同步数据。 此同步可用于创建混合应用程序。 如果有现有的本地数据库服务器，但想要将数据移到更靠近最终用户的区域，那么此方法很有吸引力。
- 多云同步：对于复杂的云解决方案，使用复制中数据在 Azure Database for MySQL 和不同云提供程序之间同步数据，包括虚拟机和托管在这些云中的数据库服务。

## <a name="limitations-and-considerations"></a>限制和注意事项

### <a name="data-not-replicated"></a>不会复制的数据
不会复制主服务器上的 [Mysql 系统数据库](https://dev.mysql.com/doc/refman/5.7/en/system-database.html)。 不会复制对主服务器上的帐户和权限所做的更改。 如果在主服务器上创建帐户，并且此帐户需要访问副本服务器，则在副本服务器端手动创建相同帐户。 若要了解哪些表包含在系统数据库中，请参阅 [MySQL 手册](https://dev.mysql.com/doc/refman/5.7/en/system-database.html)。

### <a name="requirements"></a>要求
- 主服务器版本必须至少是 MySQL 5.6 版。 
- 主服务器版本和副本服务器版本必须相同。 例如，两者必须同时是 MySQL 5.6 版或 MySQL 5.7 版。
- 每个表都必须有主键。
- 主服务器应使用 MySQL InnoDB 引擎。
- 用户必须有权配置二进制日志记录并在主服务器上创建新用户。

### <a name="other"></a>其他
- 不支持全局事务标识符 (GTID)。

## <a name="next-steps"></a>后续步骤
- 了解如何[设置复制中数据](howto-data-in-replication.md)
