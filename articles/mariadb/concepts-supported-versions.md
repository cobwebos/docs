---
title: Azure Database for MariaDB 中支持的版本
description: 说明 Azure Database for MariaDB 中支持的版本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e5d1dbc8c212d4cdefb12fb740a454324d3adfa1
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962920"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>支持的 Azure Database for MariaDB 服务器版本

已使用 InnoDB 引擎从开源[MariaDB 服务器](https://downloads.mariadb.org/)开发 Azure Database for MariaDB。 

MariaDB 使用 X.x.x.x 命名方案。 X 是主要版本，Y 是次版本，Z 是修补程序版本。

> [!NOTE]
> 在服务中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MariaDB 版本，而不是 MariaDB 服务器实例上运行的实际版本。 若要确定 MariaDB 服务器实例的版本，请使用`SELECT VERSION();`命令。

Azure Database for MariaDB 目前支持以下版本：

## <a name="mariadb-version-102"></a>MariaDB 版本 10.2

修补程序版本：10.2.25

若要详细了解此版本中的改进和修复，请参阅[MariaDB 文档](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/)。

## <a name="mariadb-version-103"></a>MariaDB 版本 10.3

修补程序版本：10.3.16

若要详细了解此版本中的改进和修复，请参阅[MariaDB 文档](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
该服务自动管理修补程序更新的升级。 例如，10.2.21 到10.2.23。  

目前，不支持次版本和主版本升级。 例如，不支持从 MariaDB 10.2 升级到 MariaDB 10.3。 如果要从10.2 升级到10.3，请创建[转储，并](./howto-migrate-dump-restore.md)将其还原到使用新引擎版本创建的服务器。

## <a name="next-steps"></a>后续步骤

- 有关基于服务层级的具体资源配额和限制的信息，请参阅[服务层级](./concepts-pricing-tiers.md)。
