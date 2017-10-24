---
title: "如何在用于 MySQL 的 Azure 数据库中配置服务器参数 | Microsoft 文档"
description: "本文介绍如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置 MySQL 服务器参数。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/10/2017
ms.openlocfilehash: a18f163cbea0dac0d8272eaa24d0d2e03542c6ca
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置服务器参数

用于 MySQL 的 Azure 数据库支持配置某些服务器参数。 本主题介绍如何使用 Azure 门户配置这些参数。 并非所有服务器参数都可调整。 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>在 Azure 门户中导航到“服务器参数”
1. 登录到 Azure 门户，然后定位到适用于 MySQL 服务器的 Azure 数据库。
2. 在“设置”部分下面，单击“服务器参数”，打开适用于 MySQL 的 Azure 数据库的“服务器参数”页。
3. 定位需要调整的任何设置。 查看“说明”列，了解用途和允许的值。 
4. 单击“保存”，保存更改。

![Azure 门户中的服务器参数边栏选项卡](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>可配置的服务器参数列表

受支持服务器参数的列表还在不断增加。 在 Azure 门户中使用服务器参数选项卡，以根据应用程序要求获取定义并配置服务器参数。 

## <a name="nonconfigurable-server-parameters"></a>不可配置的服务器参数

下列参数无法配置并且与[定价层](concepts-service-tiers.md)关联。 

| **定价层** | InnoDB 缓冲池 (MB) | 最大连接数 |
| :------------------------ | :-------- | :----------- |
| 基本 50 | 1024 | 50 | 
| 基本 100  | 2560 | 100 | 
| 标准 100 | 2560 | 200 | 
| 标准 200 | 5120 | 400 | 
| 标准 400 | 10240 | 400 | 
| 标准 800 | 20480 | 1600 |

版本 [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) 和 [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) 的其他服务器参数默认值。

## <a name="next-steps"></a>后续步骤
- [适用于 MySQL 的 Azure 数据库的连接库](concepts-connection-libraries.md)。
