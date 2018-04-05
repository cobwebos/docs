---
title: Azure Database for MySQL 中支持的版本
description: 说明 Azure Database for MySQL 中支持的版本。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: cfebdbe7485f0ffaa15828803d72c2a3f97c118d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>支持的 Azure Database for MySQL 服务器版本
使用 InnoDB 引擎通过 [MySQL Community Edition](https://www.mysql.com/products/community/) 开发 Azure Database for MySQL。  Azure Database for MySQL 目前支持以下版本：

## <a name="mysql-version-5638"></a>MySQL 5.6.38 版
若要详细了解 MySQL 5.6.38 中的改进和修复，请参阅 MySQL [文档](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html)。

## <a name="mysql-version-5720"></a>MySQL 5.7.20 版
若要了解 MySQL 5.7.20 中的改进和修复，请参阅 MySQL [文档](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.htmll)。

> [!NOTE]
> 在服务中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MySQL 版本，而不是 MySQL 服务器实例上运行的实际版本。 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。 

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
该服务会自动管理针对次要版本更新的修补。 不支持主要版本升级（例如， 从 MySQL 5.6 升级到 MySQL 5.7）。

## <a name="next-steps"></a>后续步骤

有关基于服务层的具体资源配额和限制的信息，请参阅[服务层](./concepts-pricing-tiers.md)
