---
title: Azure Database for PostgreSQL - 单一服务器中支持的版本
description: 介绍了 Azure Database for PostgreSQL - 单一服务器中支持的版本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: c328f0dc510c1b7d36b547e6560a292f98d72f6f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448045"
---
# <a name="supported-postgresql-database-versions"></a>支持的 PostgreSQL 数据库版本
Microsoft 致力于 PostgreSQL 的单个服务器在 Azure 数据库中支持 n-2 版本的 PostgreSQL 引擎。 这些版本将是 Azure 上的当前主版本 (n) 和之前的两个主版本 (-2)。

Azure Database for PostgreSQL 目前支持以下版本：

## <a name="postgresql-version-112"></a>PostgreSQL 版本 11.2
若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/11/static/release-11-2.html)。

>[!NOTE]
> PostgreSQL 版本 11 目前以预览版提供。 使用 Azure 门户创建支持即将推出，并可能尚不可用你的区域中。 可以使用[Azure CLI](quickstart-create-server-database-azure-cli.md)在任何区域中创建一个 Postgres 11 服务器。 例如，`az postgres server create -g group -n server -u username -p password -l westeurope --sku-name GP_Gen5_2 --version 11` 。

## <a name="postgresql-version-107"></a>PostgreSQL 版本 10.7
若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/10/static/release-10-7.html)。

## <a name="postgresql-version-9612"></a>PostgreSQL 版本 9.6.12
若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html)。

## <a name="postgresql-version-9516"></a>PostgreSQL 版本 9.5.16
若要了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
Azure Database for PostgreSQL 自动管理次要版本修补程序。 目前不支持主版本升级。 例如，不支持从 PostgreSQL 9.5 升级到 PostgreSQL 9.6。 如果要升级到下一主版本，请创建数据库[转储并将其还原](./howto-migrate-using-dump-and-restore.md)到使用新引擎版本创建的服务器。

> 请注意，在 PostgreSQL 版本 10、 之前[PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)被视为_主版本_升级为增加的第一个_或_数目 （对于第二个示例中，被视为 9.5 到 9.6_主要_版本升级)。
> 从版本 10 中的第一个数字的唯一更改被视为主版本升级 (例如，是 10.0 到 10.1_次要_版本升级和 10 到 11_主要_版本升级)。

## <a name="next-steps"></a>后续步骤
有关不同 PostgreSQL 扩展支持的信息，请参阅 [PostgreSQL 扩展](concepts-extensions.md)。
