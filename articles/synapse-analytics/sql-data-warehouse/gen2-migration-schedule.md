---
title: 将 SQL 池迁移到第 2 代
description: 有关将现有 SQL 池迁移到 Gen2 的说明以及按区域进行的迁移计划。
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4714d5908fffb6f5c1440c3ec512fb8173da4b57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346768"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>将 SQL 池升级到第 2 代

Microsoft 正在帮助降低运行 SQL 池的入门级成本。  能够处理要求苛刻查询的较低计算层现在可用于 SQL 池。 请阅读完整的公告：[针对 Gen2 的较低计算层级支持](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)。 新套餐在下表所示区域提供。 对于受支持的区域，可以通过：

- **自动升级过程：** 一旦服务在区域中可用，自动升级就不会启动。  当在特定区域开始自动升级时，将在您选择的维护计划期间进行单个数据仓库升级。
- [**自我升级到第 2 代：**](#self-upgrade-to-gen2)您可以通过对 Gen2 进行自我升级来控制何时升级。 如果你的区域尚不受支持，可以从某个还原点直接还原到受支持区域中的 Gen2 实例。

## <a name="automated-schedule-and-region-availability-table"></a>自动计划和区域可用性表

下表按地区汇总了较低的 Gen2 计算层可用的时间以及启动自动升级的时间。 日期随时会变化。 可返回查看，了解所在地区的可用时间。

\* 表示该区域的特定时间表当前不可用。

| **地区** | **较低的 Gen2 可用** | **自动升级开始时间** |
|:--- |:--- |:--- |
| 加拿大东部 |2020年6月1日 |2020年7月1日 |
| 中国东部 |\* |\* |
| 中国北部 |\* |\* |
| 德国中部 |\* |\* |
| 德国中西部 |可用 |2020年5月1日 |
| 印度西部 |可用 |2020年5月1日  |

## <a name="automatic-upgrade-process"></a>自动升级过程

我们会根据上面的可用性图表，为你的 Gen1 实例安排自动升级。 为了避免 SQL 池可用性出现任何意外中断，将在维护计划期间安排自动升级。 在正自动升级到 Gen2 的区域，将禁用新建 Gen1 实例的功能。 自动升级完成后，将弃用 Gen1。 有关计划的详细信息，请参阅[查看维护计划](maintenance-scheduling.md#view-a-maintenance-schedule)

当我们重新启动 SQL 池时，升级过程将涉及连接性短暂下降（大约 5 分钟）。  重新启动 SQL 池后，它将完全可用。 但是，升级过程继续在后台升级数据文件时，可能会出现性能下降的情况。 性能下降的总时间将根据数据文件的大小而有所不同。

还可以通过在重启后使用更大的 SLO 和资源类在所有主列存储表上运行 [Alter Index rebuild](sql-data-warehouse-tables-index.md) 来加快数据文件升级过程。

> [!NOTE]
> Alter Index rebuild 是一项脱机操作，在重新生成完成之前，这些表将不可用。

## <a name="self-upgrade-to-gen2"></a>自行升级至 Gen2

您可以通过在现有的 Gen1 SQL 池上执行这些步骤来选择自我升级。 如果选择自行升级，则必须在自动升级过程开始之前在所在区域完成它。 这样做可确保避免任何导致冲突的自动升级风险。

进行自行升级时有两种选择。  您可以就地升级当前 SQL 池，也可以将 Gen1 SQL 池还原到 Gen2 实例中。

- [就地升级](upgrade-to-latest-generation.md)- 此选项会将现有第 1 代 SQL 池升级到第 2 代。 当我们重新启动 SQL 池时，升级过程将涉及连接性短暂下降（大约 5 分钟）。  重新启动 SQL 池后，它将完全可用。 如果在升级过程中遇到问题，请打开[支持请求](sql-data-warehouse-get-started-create-support-ticket.md)，并将"Gen2 升级"作为可能的原因。
- [从还原点升级](sql-data-warehouse-restore-points.md)- 在当前 Gen1 SQL 池上创建用户定义的还原点，然后直接还原到 Gen2 实例。 现有的第 1 代 SQL 池将保持不变。 还原完成后，您的 Gen2 SQL 池将完全可用。  在已还原的 Gen2 实例上运行所有测试和验证过程后，可以删除原始 Gen1 实例。

   - 步骤 1：从 Azure 门户[创建用户定义的还原点](sql-data-warehouse-restore-active-paused-dw.md)。
   - 步骤 2：从用户定义的还原点还原时，将"性能级别"设置为首选的 Gen2 层。

升级过程继续在后台升级数据文件时，可能会经历一段时间的性能下降。 性能下降的总时间将根据数据文件的大小而有所不同。

要加快后台数据迁移进程，可以通过以更大的 SLO 和资源类对要查询的所有主要列存储表运行 [Alter Index rebuild](sql-data-warehouse-tables-index.md) 来立即强制数据移动。

> [!NOTE]
> Alter Index rebuild 是一项脱机操作，在重新生成完成之前，这些表将不可用。

如果 SQL 池遇到任何问题，请创建[支持请求](sql-data-warehouse-get-started-create-support-ticket.md)，并将"Gen2 升级"作为可能的原因。

有关详细信息，请参阅[升级到 Gen2](upgrade-to-latest-generation.md)。

## <a name="migration-frequently-asked-questions"></a>迁移常见问题

**问：第 2 代的成本是否与第 1 代相同？**

- 答：是的。

**问：升级将如何影响我的自动化脚本？**

- 答：任何引用服务级别目标的自动化脚本都应更改为对应于第 2 代等效项。  详见[此处](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal)。

**问：自升级通常需要多长时间？**

- 答：您可以就地升级或从还原点升级。  
   - 升级到位将导致 SQL 池暂时暂停和恢复。  SQL 池联机时，后台进程将继续。  
   - 如果要通过还原点进行升级，则需要更长时间，因为升级将完成整个还原过程。

**问：自动升级需要多长时间？**

- 答：升级的实际停机时间只是暂停和恢复服务所需的时间，即 5 到 10 分钟。 在短暂的停机时间之后，后台进程将运行存储迁移。 后台进程的时间长度取决于 SQL 池的大小。

**问：何时进行自动升级？**

- 答：在维护计划期间。 利用选择的维护计划可以最大限度地减少对业务的干扰。

**问：如果我的背景升级过程似乎卡住了，我该怎么办？**

 - 答：启动列存储表的重新索引。 请注意，在此操作期间，为表重新编制索引将处于脱机状态。

**问：如果 Gen2 没有第 1 代上的服务级别目标怎么办？**
- 答：如果您在第 1 代上运行 DW600 或 DW1200，建议分别使用 DW500c 或 DW1000c，因为第 2 代提供的内存、资源和性能高于第 1 代。

**问：我可以禁用异地备份吗？**
- 答：没有。 地理备份是一项企业功能，用于在区域不可用时保留 SQL 池可用性。 若有其他疑虑，请创建[支持请求](sql-data-warehouse-get-started-create-support-ticket.md)。

**问：第 1 代和 Gen2 之间的 T-SQL 语法是否有差异？**

- 答：从 Gen1 到 Gen2 的 T-SQL 语言语法没有变化。

**问：第 2 代是否支持维护窗口？**

- 答：是的。

**问：升级区域后，我能否创建新的 Gen1 实例？**

- 答：没有。 区域升级后，将禁用新 Gen1 实例的创建。

## <a name="next-steps"></a>后续步骤

- [升级步骤](upgrade-to-latest-generation.md)
- [维护时段](maintenance-scheduling.md)
- [资源运行状况监视器](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [开始迁移前查看](upgrade-to-latest-generation.md#before-you-begin)
- [就地升级和从还原点升级](upgrade-to-latest-generation.md)
- [创建用户定义的还原点](sql-data-warehouse-restore-points.md)
- [了解如何还原到 Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [创建 SQL 数据仓库支持请求](https://go.microsoft.com/fwlink/?linkid=857950)
