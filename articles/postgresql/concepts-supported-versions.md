---
title: 支持的版本-Azure Database for PostgreSQL-单服务器
description: 介绍 Azure Database for PostgreSQL-单服务器中支持的 Postgres 主版本和次版本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792235"
---
# <a name="supported-postgresql-major-versions"></a>支持的 PostgreSQL 主版本
Microsoft 旨在支持 Azure Database for PostgreSQL 单服务器中的 PostgreSQL 引擎的 n-2 版本。 这些版本将是 Azure 上的当前主版本 (n) 和之前的两个主版本 (-2)。

Azure Database for PostgreSQL 当前支持以下主要版本：

## <a name="postgresql-version-11"></a>PostgreSQL 版本11
当前次要版本为11.5。 请参阅[PostgreSQL 文档](https://www.postgresql.org/docs/11/static/release-11-5.html)，详细了解此次要版本中的改进和修复。

## <a name="postgresql-version-10"></a>PostgreSQL 版本10
当前次要版本为10.10。 请参阅[PostgreSQL 文档](https://www.postgresql.org/docs/10/static/release-10-10.html)，详细了解此次要版本中的改进和修复。

## <a name="postgresql-version-96"></a>PostgreSQL 版本9。6
当前次要版本为9.6.15。 请参阅[PostgreSQL 文档](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html)，详细了解此次要版本中的改进和修复。

## <a name="postgresql-version-95"></a>PostgreSQL 版本9。5
当前次要版本为9.5.19。 请参阅[PostgreSQL 文档](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html)，了解此次要版本中的改进和修复。

## <a name="managing-upgrades"></a>管理升级
PostgreSQL 项目定期发出少量版本来修复已报告的 bug。 在服务的每月部署过程中，Azure Database for PostgreSQL 会自动修补具有次要版本的服务器。 

不支持主版本自动升级。 例如，没有从 PostgreSQL 9.5 到 PostgreSQL 9.6 的自动升级。 若要升级到下一主版本，请创建数据库[转储并将其还原](./howto-migrate-using-dump-and-restore.md)到使用新引擎版本创建的服务器。

### <a name="version-syntax"></a>版本语法
在 PostgreSQL 版本10之前， [PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)将_主要版本_升级视为第一个_或_第二个数字的增长。 例如，9.5 到9.6 被视为_主要_版本升级。 从版本10开始，只有第一个数字的更改被视为主要版本升级。 例如，10.0 到10.1 是_次要_版本升级。 版本10到11是_主要_版本升级。

## <a name="next-steps"></a>后续步骤
有关支持的 PostgreSQL 扩展的信息，请参阅[扩展文档](concepts-extensions.md)。
