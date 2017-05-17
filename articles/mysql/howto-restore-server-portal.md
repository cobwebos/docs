---
title: "如何在 Azure Database for MySQL 中还原服务器 | Microsoft Docs"
description: "本文介绍如何使用 Azure 门户在 Azure Database for MySQL 中还原服务器。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0603119da20e74b423072ce6afdb8c9f20830383
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>如何使用 Azure 门户在 Azure Database for MySQL 中备份和还原服务器

## <a name="backup-happens-automatically"></a>自动进行备份
使用 Azure Database for MySQL 时，数据库服务每 5 分钟自动备份一次服务。 

如果使用的是基本层，备份将保留 7 天；如果使用的是标准层，则备份将保留 35 天。 有关详细信息，请参阅 [Azure Database for MySQL 服务层](concepts-service-tiers.md)

通过此自动备份功能，用户可将服务器及其所有数据库还原到某个较早时间点，并还原到新服务器。

## <a name="restore-in-the-azure-portal"></a>在 Azure 门户中还原
使用 Azure Database for MySQL 可以将服务器还原到某个时间点，并还原到服务器的新副本。 可以使用此新的服务器来恢复数据。 

例如，如果今天中午不小心删除了一张表，则可以将该表还原到中午之前的状态，并从服务器的新副本中检索丢失的表和数据。

以下步骤演示将示例服务器还原到某个时间点：

1. 登录到 [Azure 门户](https://portal.azure.com/)

2. 找到 Azure Database for MySQL 服务器。 在左侧窗格中，选择“所有资源”，然后从列表中选择服务器。

3.    在服务器概述边栏选项卡的顶部，单击工具栏上的“还原”。 此时将打开“还原”边栏选项卡。
![单击“还原”按钮](./media/howto-restore-server-portal/click-restore-button.png)

4. 使用必需信息填写“还原”窗体：

- “还原点(UTC)”：使用日期选取器和时间选取器选择希望还原到的时间点。 所指定的时间采用 UTC 格式，因此可能需要将本地时间转换为 UTC。
- “还原到新服务器”：输入要将现有服务器还原到的新服务器的名称。
- “位置”：系统将使用源服务器区域自动填充区域选项，且无法进行更改。
- “定价层”：系统将使用与源服务器相同的定价层自动填充定价层选项，且此处无法进行更改。 
![PITR 还原](./media/howto-restore-server-portal/pitr-restore.png)

5. 单击“确定”，将服务器还原到某个时间点。 

6. 还原完成后，找到创建的新服务器，以验证数据库已按预期还原。

## <a name="next-steps"></a>后续步骤
- [Azure Database for MySQL 的连接库](concepts-connection-libraries.md)
