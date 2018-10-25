---
title: 在最短的停机时间内迁移到 Azure Database for MySQL
description: 本文介绍如何使用 Azure 数据库迁移服务执行 MySQL 数据库到 Azure Database for MySQL 的最短停机时间迁移。
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: cb2e1a34748875de9c7f8dd59be27c408ec82818
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090325"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>在最短的停机时间内迁移到 Azure Database for MySQL
可以使用为 [Azure 数据库迁移服务](https://aka.ms/get-dms) (DMS) 新引入的**连续同步功能**以最短停机时间执行 MySQL 到 Azure Database for MySQL 的迁移。 此功能可限制应用程序引起的停机时间量。

## <a name="overview"></a>概述
DMS 将本地内容初始加载到 Azure Database for MySQL，然后在应用程序仍在运行时不断地将任何新事务同步到 Azure。 在目标 Azure 端的数据已与本地端数据保持同步后，可暂停应用程序一段时间（最短停机时间），等待最后一批数据（从停止应用程序时起，直到应用程序实际不可用于接收任何新流量）同步到目标，然后更新连接字符串使其指向 Azure。 完成后，应用程序将在 Azure 上运行！

![使用 Azure 数据库迁移服务进行连续同步](./media/howto-migrate-online/ContinuousSync.png)

- MySQL 源的 DMS 迁移当前处于公开预览状态。 如果想要试用迁移 MySQL 工作负载的服务，请继续访问门户。 你的反馈对于帮助进一步改进服务非常重要。

## <a name="next-steps"></a>后续步骤
- 观看视频[轻松将 MySQL/PostgreSQL 应用迁移到 Azure 托管服务](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)，其中包含一个演示，演示如何将 MySQL 应用迁移到 Azure Database for MySQL。
- 请参阅教程[使用 DMS 以联机方式将 MySQL 迁移到 Azure Database for MySQL](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)。
