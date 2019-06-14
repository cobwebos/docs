---
title: Azure Database for MariaDB 中支持的版本
description: 说明 Azure Database for MariaDB 中支持的版本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065722"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>支持的 Azure Database for MariaDB 服务器版本

已使用 InnoDB 引擎从开放源代码 [MariaDB 服务器](https://downloads.mariadb.org/)开发了 Azure Database for MariaDB。 Azure Database for MariaDB 目前支持以下版本：

## <a name="mariadb-version-102"></a>MariaDB 版本 10.2

请参阅[MariaDB 文档](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/)若要了解有关 MariaDB 10.2.23 中改进和修复的详细信息。

## <a name="mariadb-version-103"></a>MariaDB 版本 10.3

请参阅[MariaDB 文档](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/)若要了解有关 MariaDB 10.3.14 中改进和修复的详细信息。

> [!NOTE]
> 在服务中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MariaDB 版本，而不是 MariaDB 服务器实例上运行的实际版本。 若要确定 MariaDB 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级

该服务会自动管理针对次要版本更新的修补。

## <a name="next-steps"></a>后续步骤

- 有关基于服务层级  的具体资源配额和限制的信息，请参阅[服务层级](./concepts-pricing-tiers.md)。
