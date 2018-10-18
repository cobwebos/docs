---
title: Azure Database for PostgreSQL 中支持的版本
description: 说明 Azure Database for PostgreSQL 中支持的版本。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630563"
---
# <a name="supported-postgresql-database-versions"></a>支持的 PostgreSQL Database 版本
Microsoft 计划在 Azure Database for PostgreSQL 服务中支持 n-2 版本的 PostgreSQL 引擎，即当前发布的主要版本 (n) 和两个主要版本 (-2)。

Azure Database for PostgreSQL 目前支持以下版本：

## <a name="postgresql-version-104"></a>PostgreSQL 版本 10.4
若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/10/static/release-10-4.html)。

## <a name="postgresql-version-969"></a>PostgreSQL 版本 9.6.9
若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html)。

## <a name="postgresql-version-9513"></a>PostgreSQL 版本 9.5.13
若要了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
Azure Database for PostgreSQL 自动管理次要版本更新的修补。 目前不支持主版本升级。 例如，不支持从 PostgreSQL 9.5 升级到 PostgreSQL 9.6。 如果要升级到下一主要版本，请将其[转储和还原](./howto-migrate-using-dump-and-restore.md)到使用新引擎版本创建的服务器。

## <a name="next-steps"></a>后续步骤
有关不同 PostgreSQL 扩展支持的信息，请参阅 [PostgreSQL 扩展](concepts-extensions.md)。
