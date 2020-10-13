---
title: 服务器概念-Azure Database for MySQL 灵活的服务器
description: 本主题提供有关使用 Azure Database for MySQL 灵活服务器的注意事项和指南
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7217817e9add6214c2da8362a2769cad0c2cf330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934401"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Azure Database for MySQL 灵活的服务器 (预览版中的服务器概念) 

> [!IMPORTANT] 
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文提供了有关使用 Azure Database for MySQL 灵活服务器的注意事项和指南。

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>什么是 Azure Database for MySQL 灵活的服务器？

Azure Database for MySQL 灵活的服务器是运行社区版本 MySQL 的完全托管的数据库服务。 通常，该服务旨在根据用户要求提供灵活性和配置自定义。 它的 MySQL 服务器构造与本地环境中用户可能比较熟悉的构造相同。 具体而言，灵活的服务器是托管的，提供全新的性能、更好的服务器可管理性和控制能力，并在服务器级别公开访问和功能。

Azure Database for MySQL 灵活的服务器：

- 在 Azure 订阅中创建。
- 是数据库的父资源。
- 允许通过服务器参数公开 MySQL 配置， () 的服务器参数概念的链接。
- 执行自动备份并支持时间点还原。
- 为数据库提供了一个命名空间。
- 是具有强生存期语义的容器 - 删除服务器时会删除所包含的数据库。
- 并置区域中的资源。
- 支持客户提供的服务器维护计划
- 能够在区域冗余设置中部署灵活的服务器以提高高可用性
- 为数据库服务器访问提供虚拟网络集成
- 通过在不使用灵活的服务器时将其暂停，提供节约成本的方法
- 提供应用于其数据库的管理策略的作用域：登录名、防火墙、用户、角色、配置等。
- 版本 MySQL 5.7 当前支持。 有关详细信息，请参阅 [支持的 Azure Database for MySQL 引擎版本](./concepts-supported-versions.md)。

在 Azure Database for MySQL 灵活的服务器中，可以创建一个或多个数据库。 可以选择为每个服务器创建单一数据库来使用所有资源，也可以选择创建多个数据库来共享资源。 根据计算层的配置、Vcore 和存储 (GB) ，定价按服务器构造。 有关详细信息，请参阅 [计算和存储](./concepts-compute-storage.md)。

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>停止/启动 Azure Database for MySQL 灵活的服务器

Azure Database for MySQL 灵活的服务器使你能够在未使用时 **停止** 服务器，并在恢复活动时 **启动** 服务器。 这实质上是为了节省数据库服务器的成本，并只为资源在使用时付费。 对于开发测试工作负荷，以及在一天中仅使用服务器，这一点更重要。 停止服务器时，将删除所有活动连接。 稍后，当你想要使服务器重新联机时，可以使用 [Azure 门户](how-to-stop-start-server-portal.md) 或 CLI。

当服务器处于 **停止** 状态时，服务器的计算不会计费。 但是，当服务器的存储保持不变时，存储将继续计费，以确保在重新启动服务器时数据文件可用。

> [!IMPORTANT]
> 当你 **停止** 服务器时，它将在延伸后7天内保持该状态。 如果在此期间未手动 **启动** 该服务，则服务器将在7天结束时自动启动。 如果不使用服务器，可以选择将其 **停止** 。

在停止服务器期间，不能在该服务器上执行任何管理操作。 若要更改服务器上的任何配置设置，需要 [启动服务器](how-to-stop-start-server-portal.md)。 请参阅 [停止/启动限制](./concepts-limitations.md#stopstart-operation)。

## <a name="how-do-i-manage-a-server"></a>如何管理服务器？

您可以通过使用 [Azure 门户](./quickstart-create-server-portal.md) 或 [Azure CLI](./quickstart-create-server-cli.md)来管理 Azure Database for MySQL 灵活的服务器。

## <a name="next-steps"></a>后续步骤

-   了解 [创建服务器](./quickstart-create-server-portal.md)
-   了解 [监视和警报](./how-to-alert-on-metric.md)

