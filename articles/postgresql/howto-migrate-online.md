---
title: 在最短的停机时间内迁移到 Azure Database for PostgreSQL
description: 本文介绍如何使用 Azure 数据库迁移服务执行 PostgreSQL 数据库到 Azure Database for PostgreSQL 的最短停机时间迁移。
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 80e5d30677735b35d90fda6288d7bf6f2ea4aa1b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093827"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>在最短的停机时间内迁移到 Azure Database for PostgreSQL
可以使用为 [Azure 数据库迁移服务](https://aka.ms/get-dms) (DMS) 新引入的**连续同步功能**以最短停机时间执行 PostgreSQL 到 Azure Database for PostgreSQL 的迁移。 此功能可限制应用程序引起的停机时间量。

## <a name="overview"></a>概述
DMS 将本地内容初始加载到 Azure Database for PostgreSQL，然后在应用程序仍在运行时不断地将任何新事务同步到 Azure。 在目标 Azure 端的数据已与本地端数据保持同步后，可暂停应用程序一段时间（最短停机时间），等待最后一批数据（从停止应用程序时起，直到应用程序实际不可用于接收任何新流量）同步到目标，然后更新连接字符串使其指向 Azure。 完成后，应用程序将在 Azure 上运行！

![使用 Azure 数据库迁移服务进行连续同步](./media/howto-migrate-online/ContinuousSync.png)

PostgreSQL 源的 DMS 迁移当前处于预览状态。 如果想要试用用于迁移 PostgreSQL 工作负荷的服务，请通过 Azure DMS [预览页](https://aka.ms/dms-preview)注册以表达你的兴趣。 你的反馈对于帮助进一步改进服务非常重要。

## <a name="next-steps"></a>后续步骤
- 观看视频[使用 Microsoft Azure 实现应用现代化](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)，其中包含一个演示，演示如何将 PostgreSQL 应用迁移到 Azure Database for PostgreSQL。
- 请参阅教程[使用 DMS 以联机方式将 PostgreSQL 迁移到 Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)。
