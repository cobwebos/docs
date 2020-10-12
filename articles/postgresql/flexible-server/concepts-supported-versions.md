---
title: 支持的版本-Azure Database for PostgreSQL-灵活的服务器
description: 介绍 Azure Database for PostgreSQL 灵活的服务器中受支持的 Postgres 主版本和次要版本。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 9d651a38361ccaa7f8788fb4a375b39b2953228b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933333"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活的服务器中支持的 PostgreSQL 主版本

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 灵活的服务器当前支持以下主要版本：

## <a name="postgresql-version-12"></a>PostgreSQL 版本12

当前次要版本为12.1。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/12/static/release-12-1.html)。

## <a name="postgresql-version-11"></a>PostgreSQL 版本 11

当前次要版本为11.8。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/11/static/release-11-8.html)。

## <a name="postgresql-version-10-and-older"></a>PostgreSQL 版本10和更低版本

对于 Azure Database for PostgreSQL 灵活的服务器，我们不支持 PostgreSQL 版本10和更低版本。 如果需要较旧的版本，请使用 " [单一服务器](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) 部署" 选项。

## <a name="managing-upgrades"></a>管理升级

PostgreSQL 项目定期发布次要版本来修复已报告的 bug。 Azure Database for PostgreSQL 在服务的每月部署期间会自动使用次要版本修补服务器。

尚不支持自动主要版本升级。 例如，当前没有从 PostgreSQL 11 到 PostgreSQL 12 的自动升级。<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
