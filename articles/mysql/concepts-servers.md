---
title: 服务器概念-Azure Database for MySQL
description: 本主题提供使用 Azure Database for MySQL 服务器的注意事项和指南。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1a6aabe7ef3500a114525fe6c8bc993826295e36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275347"
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

在 Azure Database for MySQL 数据库中，可创建一个或多个数据库。 可以选择为每个服务器创建单一数据库来使用所有资源，也可以选择创建多个数据库来共享资源。 按服务器根据定价层、vCore 和存储 (GB) 的配置采用结构化定价。 有关详细信息，请参阅[定价层](./concepts-service-tiers.md)。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>如何连接到 Azure Database for MySQL 服务器并进行身份验证？

以下元素有助于确保安全地访问数据库。

|     |     |
| :-- | :-- |
| **身份验证和授权** | Azure Database for MySQL 服务器支持本机 MySQL 身份验证。 可使用服务器的管理员登录名连接到服务器并进行身份验证。 |
| 协议  | 该服务支持 MySQL 使用的基于消息的协议。 |
| **TCP/IP** | 通过 TCP/IP 和 Unix 域套接字支持该协议。 |
| **Firewall** | 为了帮助保护数据，在用户指定具有访问权限的计算机之前，防火墙规则将禁止所有对数据库服务器的访问。 请参阅 [Azure Database for MySQL 服务器防火墙规则](./concepts-firewall-rules.md)。 |
| **SSL** | 该服务支持在应用程序和数据库服务器之间强制进行 SSL 连接。  请参阅[配置应用程序的 SSL 连接性以安全连接到 Azure Database for MySQL](./howto-configure-ssl.md)。 |

## <a name="stopstart-an-azure-database-for-mysql-preview"></a>停止/启动 Azure Database for MySQL (预览版) 

Azure Database for MySQL 使你能够在未使用时 **停止** 服务器，并在恢复活动时 **启动** 服务器。 这实质上是为了节省数据库服务器的成本，并只为资源在使用时付费。 对于开发测试工作负荷，以及在一天中仅使用服务器，这一点更重要。 停止服务器时，将删除所有活动连接。 稍后，当你想要使服务器重新联机时，可以使用 [Azure 门户](how-to-stop-start-server.md) 或 [CLI](how-to-stop-start-server.md)。

当服务器处于 **停止** 状态时，服务器的计算不会计费。 但是，当服务器的存储保持不变时，存储将继续计费，以确保在重新启动服务器时数据文件可用。

> [!IMPORTANT]
> 当你 **停止** 服务器时，它将在延伸后7天内保持该状态。 如果在此期间未手动 **启动** 该服务，则服务器将在7天结束时自动启动。 如果不使用服务器，可以选择将其 **停止** 。

在停止服务器期间，不能在该服务器上执行任何管理操作。 若要更改服务器上的任何配置设置，需要 [启动服务器](how-to-stop-start-server.md)。

### <a name="limitations-of-stopstart-operation"></a>停止/启动操作的限制
-  (源和副本) 的读取副本配置不支持。

## <a name="how-do-i-manage-a-server"></a>如何管理服务器？

可通过使用 Azure 门户或 Azure CLI 来管理 Azure Database for MySQL 服务器。

## <a name="next-steps"></a>后续步骤

- 有关该服务的概述，请参阅 [Azure Database for MySQL 概述](./overview.md)
- 有关基于服务层级的具体资源配额和限制的信息，请参阅[服务层级](./concepts-service-tiers.md)
- 有关连接到服务的信息，请参阅 [Azure Database for MySQL 的连接库](./concepts-connection-libraries.md)。
