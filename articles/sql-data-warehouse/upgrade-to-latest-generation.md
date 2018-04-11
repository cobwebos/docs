---
title: 升级到最新一代的 Azure SQL 数据仓库 | Microsoft Docs
description: 将 Azure SQL 数据仓库升级到最新一代 Azure 硬件和存储体系结构的步骤。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>在 Azure 门户中升级到最新一代的 Azure SQL 数据仓库

使用 Azure 门户将 Azure SQL 数据仓库升级为使用最新一代 Azure 硬件和存储体系结构。 通过升级，可以享有更快的性能、更好的可伸缩性，以及无限的存储用于保存列存储索引。  

## <a name="applies-to"></a>适用于
此项升级适用于“弹性优化”性能层中的数据仓库。  本文中的说明介绍如何将数据仓库从“弹性优化”性能层升级到“计算优化”性能层。 

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="before-you-begin"></a>开始之前

1. 如果要升级的“弹性优化”数据仓库已暂停，请[恢复数据仓库](pause-and-resume-compute-portal.md)。
2. 做好停机几分钟的准备。 
3. 升级过程会终止所有会话，并断开所有连接。 在升级之前，请确保查询已完成。 如果正在处理事务期间启动升级，回滚时间可能会很漫长。 

## <a name="start-the-upgrade"></a>开始升级

1. 在 Azure 门户中打开数据仓库，单击“升级到‘计算优化’”。
2. 请注意“计算优化”性能层的选项。 在升级之前，默认选项与当前级别相当。
3. 选择性能层。 在预览期，“计算优化”性能层的价格减半。
4. 单击“升级”。
5. 在 Azure 门户中检查状态。
6. 等待数据仓库的状态变为“联机”。

## <a name="rebuild-columnstore-indexes"></a>重新生成列存储索引

数据仓库联机后，可以加载数据和运行查询。 但是，性能一开始可能会变慢，因为后台进程正在将数据迁移到新硬件。 

若要强制尽快迁移数据，我们建议重新生成列存储索引。 若要执行此操作，请参阅有关[重新生成列存储索引以提高段质量](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)的指南。 

## <a name="next-steps"></a>后续步骤
数据仓库已联机。 若要使用新性能功能，请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。
 