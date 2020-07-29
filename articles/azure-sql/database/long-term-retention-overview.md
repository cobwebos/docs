---
title: 长期备份保留
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 了解 Azure SQL 数据库如何 & Azure SQL 托管实例支持通过长期保留策略将完整数据库备份存储长达10年。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 992ad40d343fcc85b6c7c8fe0ed8b083a5b08238
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84344503"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>长期保留-Azure SQL 数据库和 Azure SQL 托管实例

许多应用程序都有法规、合规性或其他业务用途，要求在 Azure SQL 数据库和 Azure SQL 托管实例[自动备份](automated-backups-overview.md)提供的7-35 天之外保留数据库备份。 通过使用长期保留（LTR）功能，你可以在 Azure Blob 存储中存储指定的 SQL 数据库和 SQL 托管实例完整备份，其中包含读访问异地冗余存储，长达10年。 然后，可以将任何备份还原为新数据库。 有关 Azure 存储冗余的详细信息，请参阅 [Azure 存储冗余](../../storage/common/storage-redundancy.md)。 

对于 Azure SQL 数据库，可以启用长时间保留，并且适用于 Azure SQL 托管实例的公共预览版有限。 本文提供长期保留的概念性概述。 若要配置长期保留，请参阅从[左向右配置 AZURE Sql 数据库](long-term-backup-retention-configure.md)和[配置 AZURE sql 托管实例 ltr](../managed-instance/long-term-backup-retention-configure.md)。 

> [!NOTE]
> 可以使用 SQL 代理作业来安排[仅复制数据库备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)作为超过 35 天的 LTR 的替代方案。


## <a name="how-long-term-retention-works"></a>长期保留的工作原理
     
长期备份保留 (LTR) 利用[自动创建](automated-backups-overview.md)的完整数据库备份来启用时间点还原 (PITR)。 如果配置了 LTR 策略，这些备份将复制到不同的 Blob 进行长期存储。 复制是后台作业，不会对数据库工作负载造成性能影响。 SQL 数据库中每个数据库的 LTR 策略还可以指定创建 LTR 备份的频率。

若要实现 LTR，可以使用以下四个参数的组合定义策略：每周备份保留 (W)、每月备份保留 (M)、每年备份保留 (Y) 和年中的周 (WeekOfYear)。 如果指定 W，则每周会将一个备份复制到长期存储。 如果指定 M，则每月的第一个备份会复制到长期存储。 如果指定 Y，则会在 WeekOfYear 指定的周将一个备份复制到长期存储。 如果配置策略时指定的 WeekOfYear 在过去，则会在下一年创建第一个 LTR 备份。 每个备份都将按照创建 LTR 备份时配置的策略参数保留在长期存储中。

> [!NOTE]
> 对 LTR 策略所做的任何更改只应用到将来的备份。 例如，如果修改了每周备份保留 (W)、每月备份保留 (M) 或每年备份保留 (Y)，则新的保留设置仅应用于新备份 不会修改现有备份的保留期。 如果目的是在保留期到期之前删除旧的 LTR 备份，则需[手动删除备份](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)。
> 

LTR 策略的示例：

-  W=0、M=0、Y=5、WeekOfYear=3

   每年的第 3 个完整备份将保留 5 年。
   
- W=0、M=3、Y=0

   每月的第一个完整备份将保留 3 个月。

- W=12、M=0、Y=0

   每个每周完整备份将保留 12 周。

- W=6、M=12、Y=10、WeekOfYear=16

   每个每周完整备份将保留 6 周。 每月的第一个完整备份例外，该备份将保留 12 个月。 每年的第 16 周创建的完整备份例外，该备份将保留 10 年。 

下表说明了以下策略的长期备份的节奏和到期：

W=12 周（84 天）、M=12 个月（365 天）、Y=10 年（3650 天）、WeekOfYear=15（4 月 15 日后的周）

   ![ltr 示例](./media/long-term-retention-overview/ltr-example.png)


如果修改以上策略并设置 W=0（无每周备份），则备份副本的节奏将更改，如上表中突出显示的日期所示。 保留这些备份所需的存储量将相应减少。 

> [!IMPORTANT]
> 单个 LTR 备份的时间由 Azure 控制。 无法手动创建 LTR 备份或控制创建备份的时间。 配置 LTR 策略后，最多可能需要 7 天才能在可用备份列表中显示第一个 LTR 备份。  


## <a name="geo-replication-and-long-term-backup-retention"></a>异地复制和长期备份保留

如果使用活动异地复制或故障转移组作为业务连续性解决方案，则应准备最终故障转移，并在辅助数据库或实例上配置相同的 LTR 策略。 如果备份不是从辅助副本生成的，则 LTR 存储成本不会增加。 仅当辅助副本成为主副本时，才会创建备份。 这样可以确保在触发故障转移以及在主数据库转移到次要区域时，不间断地生成 LTR 备份。 

> [!NOTE]
> 在发生导致故障转移的服务中断问题后恢复原始的主数据库时，该数据库将变成新的辅助数据库。 因此，在该数据库重新变成主数据库之前，备份创建操作不会恢复，并且现有的 LTR 策略不会生效。 

## <a name="sql-managed-instance-support"></a>SQL 托管实例支持

将长期备份保留用于 Azure SQL 托管实例具有以下限制：

- **受限公共预览版** - 此预览版仅适用于 EA 订阅和云解决方案提供商订阅，并受到有限可用性的限制。  
- [**仅限 PowerShell**](../managed-instance/long-term-backup-retention-configure.md) -目前没有 Azure 门户支持。 必须使用 PowerShell 启用 LTR。 

若要请求注册，请创建 [Azure 支持票证](https://azure.microsoft.com/support/create-ticket/)。 对于 "问题类型"，请选择 "服务" "服务" "SQL 托管实例"，对于 "问题类型"，请选择 "**备份"、"还原" 和 "业务连续性/长期备份保留"**。 在你的请求中，请陈述你希望注册到托管实例的 LTR 公共预览中，以便进行 SQL。

## <a name="configure-long-term-backup-retention"></a>配置长期备份保留

你可以使用适用于 Azure SQL 数据库的 Azure 门户和 PowerShell 以及适用于 Azure SQL 托管实例的 PowerShell 配置长期备份保留。 若要从 LTR 存储还原数据库，可以根据时间戳选择一个特定备份。 可以将数据库还原到与原始数据库相同的订阅下的任何现有服务器或托管实例。

若要了解如何使用 Azure 门户或 PowerShell 为 SQL 数据库的备份配置长期保留或还原数据库，请参阅[管理 AZURE Sql 数据库长期备份保留](long-term-backup-retention-configure.md)

若要了解如何使用 PowerShell 配置长期保留或还原 SQL 托管实例的数据库，请参阅[管理 AZURE sql 托管实例长期备份保留](../managed-instance/long-term-backup-retention-configure.md)。

若要从 LTR 存储还原数据库，可以根据时间戳选择一个特定备份。 数据库可以还原到原始数据库所在的订阅中的任何现有服务器。 若要了解如何使用 Azure 门户或 PowerShell 从 LTR 备份还原数据库，请参阅[管理 Azure SQL 数据库长期备份保留](long-term-backup-retention-configure.md)。 在你的请求中，请陈述你要在托管实例的 LTR 公共预览版中注册 SQL。

## <a name="next-steps"></a>后续步骤

数据库备份可保护数据免遭意外损坏或删除，因此数据库备份是任何业务连续性和灾难恢复策略不可或缺的组成部分。 若要了解其他 SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
 