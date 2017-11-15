---
title: "如何在 Azure Database for PostgreSQL 中还原服务器 | Microsoft Docs"
description: "本文介绍如何使用 Azure 门户在 Azure Database for PostgreSQL 中还原服务器。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 903fd2ff446e1963ab5cfcec745766188b74efcf
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>如何使用 Azure 门户在 Azure Database for PostgreSQL 中备份和还原服务器

## <a name="backup-happens-automatically"></a>自动进行备份
使用 Azure Database for PostgreSQL 时，数据库服务每 5 分钟自动备份一次服务。 

如果使用的是基本层，备份将保留 7 天；如果使用的是标准层，则备份将保留 35 天。 有关详细信息，请参阅 [Azure Database for PostgreSQL 服务层](concepts-service-tiers.md)

通过此自动备份功能，用户可将服务器及其所有数据库还原到某个较早时间点，并还原到新服务器。

## <a name="restore-in-the-azure-portal"></a>在 Azure 门户中还原
使用 Azure Database for PostgreSQL 可以将服务器还原到某个时间点，并还原到服务器的新副本。 可以使用此新的服务器来恢复数据。 

例如，如果今天中午不小心删除了一张表，则可以将该表还原到中午之前的状态，并从服务器的新副本中检索丢失的表和数据。

以下步骤演示将示例服务器还原到某个时间点：
1. 登录到 [Azure 门户](https://portal.azure.com/)
2. 找到 Azure Database for PostgreSQL 服务器。 在 Azure 门户的左侧菜单中单击“所有资源”，并键入服务器名称，例如“mypgserver-20170401”，以搜索现有的服务器。 单击搜索结果中列出的服务器名称。 服务器的“概述”页面随即打开，其中提供了用于进一步配置的选项。

   ![Azure 门户 - 进行搜索，找到服务器](media/postgresql-howto-restore-server-portal/1-locate.png)

3. 在服务器概述页的工具栏中，单击“还原”。 此时将打开“还原”页。

   ![Azure Database for PostgreSQL - 概述 - 还原按钮](./media/postgresql-howto-restore-server-portal/2_server.png)

4. 使用必需信息填写“还原”窗体：

   ![Azure Database for PostgreSQL - 还原信息 ](./media/postgresql-howto-restore-server-portal/3_restore.png)
  - 还原点：选择更改服务器前的时间点。
  - 目标服务器：提供一个要还原到的新服务器名称。
  - 位置：不可选择区域。 默认情况下，此值与源服务器相同。
  - 定价层：还原服务器时不能更改此值。 此值与源服务器相同。 

5. 单击“确定”，将服务器还原到某个时间点。 

6. 还原完成后，找到创建的新服务器，以验证数据是否已按预期还原。

## <a name="next-steps"></a>后续步骤
- [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)
