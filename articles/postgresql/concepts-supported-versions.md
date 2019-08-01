---
title: Azure Database for PostgreSQL - 单一服务器中支持的版本
description: 介绍了 Azure Database for PostgreSQL - 单一服务器中支持的版本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551364"
---
# <a name="supported-postgresql-database-versions"></a>支持的 PostgreSQL 数据库版本
Microsoft 旨在支持 Azure Database for PostgreSQL 单服务器中的 PostgreSQL 引擎的 n-2 版本。 这些版本将是 Azure 上的当前主版本 (n) 和之前的两个主版本 (-2)。

Azure Database for PostgreSQL 目前支持以下版本：

## <a name="postgresql-version-112"></a>PostgreSQL 版本11。2
若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/11/static/release-11-2.html)。

## <a name="postgresql-version-107"></a>PostgreSQL 版本 10.7
若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/10/static/release-10-7.html)。

## <a name="postgresql-version-9612"></a>PostgreSQL 版本 9.6.12
若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html)。

## <a name="postgresql-version-9516"></a>PostgreSQL 版本 9.5.16
若要了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
Azure Database for PostgreSQL 自动管理次要版本修补程序。 目前不支持主版本升级。 例如，不支持从 PostgreSQL 9.5 升级到 PostgreSQL 9.6。 如果要升级到下一主版本，请创建数据库[转储并将其还原](./howto-migrate-using-dump-and-restore.md)到使用新引擎版本创建的服务器。

> 请注意, 在 PostgreSQL 版本10之前, [PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)视为第一个_或_第二个数字的增加 (例如, 9.5 到9.6 被视为_主要_版本升级)。
> 从版本10开始, 只有第一个数字中的更改被视为主要版本升级 (例如, 10.0 到10.1 是_次_版本升级, 10 到11是_主要_版本升级)。

## <a name="next-steps"></a>后续步骤
有关不同 PostgreSQL 扩展支持的信息，请参阅 [PostgreSQL 扩展](concepts-extensions.md)。
