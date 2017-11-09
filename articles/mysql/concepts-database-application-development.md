---
title: "Azure Database for MySQL 数据库应用程序开发概述 | Microsoft Docs"
description: "介绍开发人员为连接 Azure Database for MySQL 而编写应用程序代码时应遵循的设计注意事项"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 6a9bd8f88383b5186e470163bc67f9233172fd49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Azure Database for MySQL 应用程序开发概述 
本文介绍开发人员为连接 Azure Database for MySQL 而编写应用程序代码时应遵循的设计注意事项。 

> [!TIP]
> 有关演示如何使用 workbench 和 mysql.exe 创建服务器、创建基于服务器的防火墙、查看服务器属性、创建数据库以及进行连接和查询的教程，请参阅[设计首个 Azure Database for MySQL 数据库](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>语言和平台
为各种编程语言和平台提供了代码示例。 可在以下位置找到代码示例的链接：[用于连接 Azure Database for MySQL 的连接库](concepts-connection-libraries.md)

## <a name="tools"></a>工具
Azure Database for MySQL 使用 MySQL 社区版，此版本与 MySQL 常用管理工具（如 Workbench）或 MySQL 实用程序（如 mysql.exe）及 [phpMyAdmin](https://www.phpmyadmin.net/) 和 [Navicat](https://www.navicat.com/products/navicat-for-mysql) 等工具兼容。 还可以使用 Azure 门户、Azure CLI 和 REST API 与数据库服务进行交互。

## <a name="resource-limitations"></a>资源限制
Azure Database for MySQL 使用两种不同机制管理可用于服务器的资源： 
- 资源调控。
- 强制实施限制。

## <a name="security"></a>安全
Azure Database for MySQL 提供用于在 MySQL Database 中限制访问、保护数据、配置用户和角色及监视活动的资源。

## <a name="authentication"></a>身份验证
Azure Database for MySQL 支持用户和登录名这两种服务器身份验证方式。

## <a name="resiliency"></a>复原能力
如果在连接到 MySQL Database 时发生暂时性错误，代码应重试调用。 建议让重试逻辑使用退让逻辑，这样就不会因为多个客户端同时重试而对 SQL 数据库造成混乱。

- 代码示例：有关演示重试逻辑的代码示例，请在以下位置参阅所选语言的示例：[用于连接 Azure Database for MySQL 的连接库](concepts-connection-libraries.md)

## <a name="managing-connections"></a>管理连接
数据库连接是一种受限资源，因此访问 MySQL Database 时，建议合理使用连接，以便实现更好的性能。
- 使用连接池或永久性连接访问数据库。
- 使用短寿命连接访问数据库。 
- 尝试连接时在应用程序中使用重试逻辑，以便捕获由于并发连接数达到允许的最大值而导致的失败。 在重试逻辑中，设置较短延迟，并在进行更多连接尝试前等待随机的一段时间。