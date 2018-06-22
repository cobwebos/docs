---
title: Azure Database for MySQL 数据复制存储过程
description: 本文介绍了所有用于数据复制的存储过程。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 2d62cd693d7a67faf836c645f8bd33de9afca949
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266102"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure Database for MySQL 数据复制存储过程

借助数据复制，可以将本地运行的 MySQL 服务器、虚拟机或其他云提供程序托管的数据库服务中的数据同步到 Azure Database for MySQL 服务。

下面的存储过程用于在主体和副本之间设置或删除数据复制。

|**存储过程名称**|**输入参数**|**输出参数**|**用法说明**|
|-----|-----|-----|-----|
|mysql.az_replication_change_primary|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|不适用|若要使用 SSL 模式传输数据，请将 CA 证书的上下文传递到 master_ssl_ca 参数中。 </br><br>若要不使用 SSL 模式传输数据，请将空字符串传递到 master_ssl_ca 参数中。|
|mysql.az_replication _start|不适用|不适用|开始复制。|
|mysql.az_replication _stop|不适用|不适用|停止复制。|
|mysql.az_replication _remove_primary|不适用|不适用|删除主体和副本之间的复制关系。|
|mysql.az_replication_skip_counter|不适用|不适用|跳过一个复制错误。|

若要在主体和 Azure Database for MySQL 副本之间设置数据复制，请参阅[如何配置数据复制](howto-data-in-replication.md)。