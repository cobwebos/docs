---
title: "Azure Database for MySQL 中的服务器概念 | Microsoft Docs"
description: "本主题提供使用 Azure Database for MySQL 服务器的注意事项和指南。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: d3de3fdf28997b63321bf23443472db43ebb5c52
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的服务器概念
本文提供使用 Azure Database for MySQL 服务器的注意事项和指南。

## <a name="what-is-an-azure-database-for-mysql-server"></a>什么是 Azure Database for MySQL 服务器？

Azure Database for MySQL 服务器是多个数据库的中心管理点。 它的 MySQL 服务器构造与本地环境中用户可能比较熟悉的构造相同。 具体而言，Azure Database for MySQL 服务是托管的服务，它提供性能保证，并公开服务器级访问权限和功能。

Azure Database for MySQL 服务器：

- 在 Azure 订阅中创建。
- 是数据库的父资源。
- 为数据库提供了一个命名空间。
- 是具有强生存期语义的容器 - 删除服务器时会删除所包含的数据库。
- 并置区域中的资源。
- 为服务器和数据库访问提供连接终结点。
- 提供应用于其数据库的管理策略的作用域：登录名、防火墙、用户、角色、配置等。
- 在多个版本内可用。 有关详细信息，请参阅[支持的 Azure Database for MySQL 数据库版本](./concepts-supported-versions.md)。

在 Azure Database for MySQL 数据库中，可创建一个或多个数据库。 可以选择为每个服务器创建单一数据库来使用所有资源，也可以选择创建多个数据库来共享资源。 按服务器根据定价层、计算单位和存储 (GB) 的配置采用结构化定价。 有关详细信息，请参阅[定价层](./concepts-service-tiers.md)。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>如何连接到 Azure Database for MySQL 服务器并进行身份验证？

以下元素有助于确保安全地访问数据库。
|||
| :-- | :-- |
| **身份验证和授权** | Azure Database for MySQL 服务器支持本机 MySQL 身份验证。 可使用服务器的管理员登录名连接到服务器并进行身份验证。 |
| **协议** | 该服务支持 MySQL 使用的基于消息的协议。 |
| TCP/IP | 通过 TCP/IP 和 Unix 域套接字支持该协议。 |
| **防火墙** | 为了帮助保护数据，在用户指定具有访问权限的计算机之前，防火墙规则将禁止所有对数据库服务器的访问。 请参阅 [Azure Database for MySQL 服务器防火墙规则](./concepts-firewall-rules.md)。 |
| SSL | 该服务支持在应用程序和数据库服务器之间强制进行 SSL 连接。  请参阅[配置应用程序的 SSL 连接性以安全连接到 Azure Database for MySQL](./howto-configure-ssl.md)。 |

## <a name="how-do-i-manage-a-server"></a>如何管理服务器？
可通过使用 Azure 门户或 Azure CLI 来管理 Azure Database for MySQL 服务器。

## <a name="next-steps"></a>后续步骤
- 有关该服务的概述，请参阅 [Azure Database for MySQL 概述](./overview.md)
- 有关基于服务层的具体资源配额和限制的信息，请参阅[服务层](./concepts-service-tiers.md)
- 有关连接到服务的信息，请参阅 [Azure Database for MySQL 的连接库](./concepts-connection-libraries.md)。
