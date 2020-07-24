---
title: 支持的版本 - Azure Database for MariaDB
description: 了解 Azure Database for MariaDB 服务支持哪些版本的 MariaDB 服务器。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: 0b495bf21b1f78e5e6a9285f67e1769d2d2a0db1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065703"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>支持的 Azure Database for MariaDB 服务器版本

已使用 InnoDB 引擎从开放源代码 [MariaDB 服务器](https://downloads.mariadb.org/)开发了 Azure Database for MariaDB。

MariaDB 使用 X.Y.Z 命名方案。 X 是主版本，Y 是次版本，Z 是修补程序版本。

> [!NOTE]
> 在服务中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MariaDB 版本，而不是 MariaDB 服务器实例上运行的实际版本。 若要确定 MariaDB 服务器实例的版本，请使用 `SELECT VERSION();` 命令。

Azure Database for MariaDB 目前支持以下版本：

## <a name="mariadb-version-102"></a>MariaDB 版本 10.2

修补程序版本：10.2.32

若要详细了解此版本中的改进和修复，请参阅 [MariaDB 文档](https://mariadb.com/kb/en/mariadb-10232-release-notes/)。

## <a name="mariadb-version-103"></a>MariaDB 版本 10.3

修补程序版本：10.3.23

若要详细了解此版本中的改进和修复，请参阅 [MariaDB 文档](https://mariadb.com/kb/en/mariadb-10323-release-notes/)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
该服务自动管理修补程序更新的升级。 例如，10.2.21 到 10.2.23。  

目前，不支持次版本和主版本升级。 例如，不支持从 MariaDB 10.2 升级到 MariaDB 10.3。 如果要从 10.2 升级到 10.3，请将其[转储和还原](./howto-migrate-dump-restore.md)到使用新引擎版本创建的服务器。

## <a name="next-steps"></a>后续步骤

- 有关基于服务层级的具体资源配额和限制的信息，请参阅[服务层级](./concepts-pricing-tiers.md)。
