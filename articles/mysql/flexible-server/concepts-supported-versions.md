---
title: 支持的版本-Azure Database for MySQL 灵活的服务器
description: 了解 Azure Database for MySQL 灵活的服务器支持的 MySQL 服务器版本
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: f29a34fd8916110355c0122fee9db29599a01231
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933356"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL-灵活服务器支持的版本


> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。


Azure Database for MySQL 灵活的服务器由 [MySQL 社区版](https://www.mysql.com/products/community/)使用 InnoDB 引擎提供支持。

MySQL 使用 X.Y.Z 命名方案。 X 是主版本，Y 是次版本，Z 是 bug 修复版本。 有关方案的详细信息，请参阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。

> [!NOTE]
> 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。

Azure Database for MySQL 目前支持以下版本：

## <a name="mysql-version-57"></a>MySQL 版本 5.7

Bug 修复版本：5.7.29

该服务执行基础硬件、OS 和数据库引擎的自动修补。 修补包括安全更新和软件更新。 对于 MySQL 引擎，次要版本升级也包括在计划内维护版本中。 用户可以将修补计划配置为系统托管或定义自己的自定义计划。 在维护计划期间，将应用补丁，并且在修补过程中，可能需要重启服务器才能完成更新。 使用自定义计划，用户可以使修补周期可预测，并选择对业务影响最小的维护时段。 一般来说，作为持续集成和发布的一部分，该服务遵循每月发布计划。

要详细了解此版本中的改进和修复，请参阅 MySQL [发行说明](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
该服务会自动管理针对 Bug 修复版本更新的修补。 例如，5.7.29 到5.7.30。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[使用 MySQL 在 Windows 上生成 PHP 应用](../../app-service/app-service-web-tutorial-php-mysql.md)<br/>
>[在 Linux 上构建带有 MySQL 的 PHP 应用](../../app-service/containers/tutorial-php-mysql-app.md)<br/>
>[用 MySQL 构建基于 Java 的弹簧应用](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>
