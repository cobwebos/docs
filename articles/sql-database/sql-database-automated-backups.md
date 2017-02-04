---
title: "SQL 数据库备份 - 自动化、异地冗余 | Microsoft 文档"
description: "SQL 数据库每隔几分钟会自动创建一个本地数据库备份，使用 Azure 读取访问异地冗余存储来提供异地冗余。"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2016
ms.author: sashan;carlrab;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: f6bb6e1c81cafe5f0e5c43c99ab15a0483742868


---
# <a name="learn-about-sql-database-backups"></a>了解 SQL 数据库备份
<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.

Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    � Definition of the feature terminology.  i.e., What is a database backup?
    � Characteristics and capabilities of the feature. (How the feature works)
    � Common uses with links to overview topics that recommend when to use the feature.
    � Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    � Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    � How to steps for using the feature (Tasks)
    � How to solve business problems that incorporate the feature (Overviews)

GUIDELINES for the H1 

    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  

    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "elastic pools", use a synonym. For example:    "Learn about elastic pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

GUIDELINES for introduction

    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

SQL 数据库会自动创建数据库备份，并使用 Azure 读取访问异地冗余存储 (RA-GRS) 来提供异地冗余。 可以自动创建这些备份且不收取额外费用。 无需执行任何操作即可创建这些备份。 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 如果想要在自己的存储容器中保存备份，可以配置长期备份保留策略。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.

    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>什么是 SQL 数据库备份？
<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->


<!----------------- 
    Explains first component of the backup feature
------------------>

SQL 数据库使用 SQL Server 技术创建[完整](https://msdn.microsoft.com/library/ms186289.aspx)、[差异](https://msdn.microsoft.com/library/ms175526.aspx)和[事务日志](https://msdn.microsoft.com/library/ms191429.aspx)备份。 一般每隔 5-10 分钟创建一次事务日志备份，具体频率取决于性能级别和数据库活动量。 借助事务日志备份以及完整备份和差异备份，可将数据库还原到托管数据库的服务器上的特定时间点。 还原数据库时，服务将会确定需要还原哪些完整、差异备份和事务日志备份。

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

可使用这些备份执行以下任务：

* 在保留期内将数据库还原到某个时间点。 此操作会在与原始数据库相同的服务器中创建新数据库。
* 将已删除的数据库还原到删除时的时间点或保留期内的任意时间点。 仅可在创建原始数据库所在的同一服务器中还原已删除的数据库。
* 将数据库还原到其他地理区域。 在无法访问服务器和数据库的情况下，此操作可帮助从地理位置灾难中恢复， 它会在全球任意位置的任意现有服务器中创建新数据库。 
* 从 Azure 恢复服务保管库中存储的特定备份还原数据库。 此操作允许还原旧版本的数据库，以满足符合性请求或运行旧版本的应用程序。 请参阅[长期保留](sql-database-long-term-retention.md)。
* 若要执行还原，请参阅[从备份还原数据库](sql-database-recovery-using-backups.md)。

> [!TIP]
> 有关教程，请参阅[数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery.md)
>

<!----------------- 
    Explains first component of the backup feature
------------------>

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

> [!NOTE]
> 在 Azure 存储中，术语*复制*是指将文件从一个位置复制到另一个位置。 SQL *数据库复制*是指让多个辅助数据库与主数据库保持同步。 
> 
> 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>免费附送的备份存储空间有多少？
SQL 数据库提供高达备份存储的最大预配数据库存储空间两倍的容量，不收取任何额外费用。 例如，如果你有一个标准数据库实例并且预配的数据库大小为 250 GB，则可以免费获得 500 GB 的备份存储空间。 如果数据库超过提供的备份存储空间，你可以选择与 Azure 支持联系来缩短保留期。 另一种做法是按照标准读取访问异地冗余存储 (RA-GRS) 费率付费购买额外的备份存储空间。 

## <a name="how-often-do-backups-happen"></a>备份频率如何？
每周执行一次完整数据库备份，通常每隔数小时执行一次差异数据库备份，通常每隔 5-10 分钟执行一次事务日志备份。 会在数据库创建后立即计划第一次完整备份。 完整备份通常可在 30 分钟内完成，但如果数据库很大，花费的时间可能更长。 例如，对已还原的数据库或数据库副本执行初始备份可能需要更长时间。 在完成首次完整备份后，将在后台以静默方式自动计划和管理所有后续备份。 在平衡整体系统工作负荷时，SQL 数据库服务会确定所有数据库备份的确切时间。 

备份存储异地复制根据 Azure 存储复制计划执行。

## <a name="how-long-do-you-keep-my-backups"></a>备份的保留时间有多长？
每个 SQL 数据库备份都有一个保留期，该期限基于数据库的[服务层](sql-database-service-tiers.md)。 各服务层中数据库的保留期如下：

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

* 基本服务层为 7 天。
* 标准服务层为 35 天。
* 高级服务层为 35 天。

如果将数据库从标准或高级服务层降级到基本服务层，备份将保存 7 天。 超过 7 天的所有现有备份将被清除。 

如果将数据库从基本服务层升级到标准或高级服务层，SQL 数据库将保存现有备份，直到超过 35 天。 创建的新备份将保存 35 天。

如果删除了某个数据库，SQL 数据库将保存其备份，就像保存联机数据库的备份一样。 例如，假设你删除了保留期为 7 天的某个基本数据库。 已保存 4 天的备份将继续保存 3 天。

> [!IMPORTANT]
> 如果删除了托管 SQL 数据库的 Azure SQL 服务器，则属于该服务器的所有数据库也将被删除且不可恢复。 无法还原已删除的服务器。
> 
> 

## <a name="how-to-extend-the-backup-retention-period"></a>如何延长备份保留期？
如果应用程序要求备份保留更长时间，可通过为每个数据库配置长期备份保留策略（LTR 策略）来延长内置保留期。 允许将内置保持期从 35 天延长为最多 10 年。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

将 LTR 策略添加到使用 Azure 门户或 API 的数据库后，每周的完整数据库备份会自动复制到自己的 Azure 备份服务保管库。 如果使用 TDE 加密数据库，备份会在静止时自动加密。  服务保管库会根据时间戳和 LTR 策略自动删除过期的备份。  因此，无需管理备份计划，也不用担心旧文件的清除工作。 只要保管库与 SQL 数据库位于同一订阅中，还原 API 就支持存储在保管库中的备份。 可以使用 Aure 门户或 PowerShell 访问这些备份。

> [!TIP]
> 有关教程，请参阅[数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery.md)
>

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>后续步骤

- 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 若要了解其他 Azure SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。
- 若要还原到某个时间点，请参阅[将数据库还原到某个点时间](sql-database-point-in-time-restore.md)。
- 若要从数据库的服务生成备份查看最早的还原点，请参阅[查看最早的还原点](sql-database-view-oldest-restore-point.md)
- 若要在 Azure 恢复服务保管库中配置自动备份的长期保留，请参阅[配置长期备份保留](sql-database-configure-long-term-retention.md)



<!--HONumber=Dec16_HO2-->


