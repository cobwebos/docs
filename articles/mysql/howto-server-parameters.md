---
title: "如何在 Azure Database for MySQL 中配置服务器参数"
description: "本文介绍如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置 MySQL 服务器参数。"
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b3510c616d2a9ba66cb83cb998c42e03fdbb0f2b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置服务器参数

用于 MySQL 的 Azure 数据库支持配置某些服务器参数。 本文介绍如何使用 Azure 门户配置这些参数。 并非所有服务器参数都可调整。 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>在 Azure 门户中导航到“服务器参数”
1. 登录到 Azure 门户，然后定位到适用于 MySQL 服务器的 Azure 数据库。
2. 在“设置”部分下面，单击“服务器参数”，打开适用于 MySQL 的 Azure 数据库的“服务器参数”页。
![Azure 门户中的服务器参数页](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. 定位需要调整的任何设置。 查看“说明”列，了解用途和允许的值。 
![枚举下拉按钮](./media/howto-server-parameters/3-toggle_parameter.png)
4. 单击“保存”，保存更改。
![保存或放弃更改](./media/howto-server-parameters/4-save_parameters.png)
5. 保存参数的新值后，随时可以通过选择“全部重置为默认设置”，将所有设置还原为默认值。
![全部重置为默认设置](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>可配置的服务器参数列表

受支持服务器参数的列表还在不断增加。 在 Azure 门户中使用服务器参数选项卡，以根据应用程序要求获取定义并配置服务器参数。 

## <a name="nonconfigurable-server-parameters"></a>不可配置的服务器参数
InnoDB 缓冲池和最大连接数不可配置，因[定价层](concepts-service-tiers.md)而定。 

|定价层| **计算代**|**vCore(s)**|InnoDB 缓冲池 (MB)| 最大连接数|
|---|---|---|---|--|
|基本| 第 4 代| 1| 1024| 50 |
|基本| 第 4 代| 2| 2560| 100 |
|基本| 第 5 代| 1| 1024| 50 |
|基本| 第 5 代| 2| 2560| 100 |
|常规用途| 第 4 代| 2| 2560| 200|
|常规用途| 第 4 代| 4| 5120| 400|
|常规用途| 第 4 代| 8| 10240| 800|
|常规用途| 第 4 代| 16| 20480| 1600|
|常规用途| 第 4 代| 32| 40960| 3200|
|常规用途| 第 5 代| 2| 2560| 200|
|常规用途| 第 5 代| 4| 5120| 400|
|常规用途| 第 5 代| 8| 10240| 800|
|常规用途| 第 5 代| 16| 20480| 1600|
|常规用途| 第 5 代| 32| 40960| 3200|
|内存优化| 第 5 代| 2| 7168| 600|
|内存优化| 第 5 代| 4| 15360| 1250|
|内存优化| 第 5 代| 8| 30720| 2500|
|内存优化| 第 5 代| 16| 62464| 5000|
|内存优化| 第 5 代| 32| 125952| 10000| 

以下附加服务器参数不可在系统中配置：

|**Parameter**|**固定值**|
| :------------------------ | :-------- |
|基本层中的 innodb_file_per_table|熄灭|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

在版本 [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) 和 [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) 中，上表中未列出的其他服务器参数将设置为其 MySQL 现成默认值。

## <a name="next-steps"></a>后续步骤
- [Azure Database for MySQL 的连接库](concepts-connection-libraries.md)。
