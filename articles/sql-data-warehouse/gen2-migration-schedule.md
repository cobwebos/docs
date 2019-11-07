---
title: 将数据仓库迁移到 Gen2
description: 有关将现有数据仓库迁移到 Gen2 以及按区域迁移计划的说明。
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 07/22/2019
ms.custom: seo-lt-2019
ms.openlocfilehash: 888f50d645c9b3babf95335e434db65423108ccb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693034"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>将数据仓库升级到 Gen2

Microsoft 正在帮助降低运行数据仓库的入门级成本。  能够处理高要求查询的较低计算层级现在适用于 Azure SQL 数据仓库。 请阅读完整的公告：[针对 Gen2 的较低计算层级支持](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)。 新套餐在下表所示区域提供。 对于支持的区域，现有的 Gen1 数据仓库可以通过以下任一方式升级到 Gen2：

- **自动升级过程：** 如果服务在某个区域中可用，则自动升级不会立即启动。  当自动升级在特定区域启动时，将在你选择的维护计划期间进行单独的 DW 升级。
- [**自我升级到 Gen2：** ](#self-upgrade-to-gen2)你可以通过执行自我升级到 Gen2 来控制升级的时间。 如果你的区域尚不受支持，可以从某个还原点直接还原到受支持区域中的 Gen2 实例。

## <a name="automated-schedule-and-region-availability-table"></a>自动计划和区域可用性表

下表按地区汇总了较低的 Gen2 计算层可用的时间以及启动自动升级的时间。 日期随时会变化。 可返回查看，了解所在地区的可用时间。

\* 表示该区域的特定时间表当前不可用。

| **区域** | **较低的 Gen2 可用** | **自动升级开始时间** |
|:--- |:--- |:--- |
| 澳大利亚东部 |可用 |完成 |
| 澳大利亚东南部 |可用 |完成 |
| 巴西南部 |可用 |完成 |
| 加拿大中部 |可用 |完成 |
| 加拿大东部 |2020年6月1日 |2020年7月1日 |
| 美国中部 |可用 |完成 |
| 中国东部 |\* |\* |
| 中国东部 2 |可用 |完成 |
| 中国北部 |\* |\* |
| 中国北部 2 |可用 |完成 |
| 东亚 |可用 |完成 |
| 美国东部 |可用 |完成 |
| 美国东部 2 |可用 |完成 |
| 法国中部 |可用 |正在进行 |
| 德国中部 |\* |\* |
| 德国中西部 |2019年9月1日|2019年10月1日 |
| 印度中部 |可用 |完成 |
| 印度南部 |可用 |完成 |
| 印度西部 |2019年7月1日 |正在进行 |
| 日本东部 |可用 |完成 |
| 日本西部 |可用 |完成 |
| 韩国中部 |可用 |完成 |
| 韩国南部 |可用 |完成 |
| 美国中北部 |可用 |完成 |
| 北欧 |可用 |完成 |
| 南非北部 |2019年7月12日 |完成 |
| 美国中南部 |可用 |完成 |
| 东南亚 |可用 |完成 |
| 阿拉伯联合酋长国北部 |2019年7月20日 |完成 |
| 英国南部 |可用 |正在进行 |
| 英国西部 |可用 |正在进行 |
| 美国中西部 |2019年11月1日 |2019年12月1日|
| 欧洲西部 |可用 |完成 |
| 美国西部 |可用 |完成 |
| 美国西部 2 |可用 |完成 |

## <a name="automatic-upgrade-process"></a>自动升级过程

我们会根据上面的可用性图表，为你的 Gen1 实例安排自动升级。 为了避免数据仓库可用性发生任何意外中断，将在维护计划期间安排自动升级。 在正自动升级到 Gen2 的区域，将禁用新建 Gen1 实例的功能。 自动升级完成后，将弃用 Gen1。 有关计划的详细信息，请参阅[查看维护计划](viewing-maintenance-schedule.md)

重启数据仓库时，升级过程会导致连接性短暂下降（大约 5 分钟）。  重启数据仓库后，它将完全可用。 但是，升级过程继续在后台升级数据文件时，可能会出现性能下降的情况。 性能下降的总时间将根据数据文件的大小而有所不同。

还可以通过在重启后使用更大的 SLO 和资源类在所有主列存储表上运行 [Alter Index rebuild](sql-data-warehouse-tables-index.md) 来加快数据文件升级过程。

> [!NOTE]
> Alter Index rebuild 是一项脱机操作，在重新生成完成之前，这些表将不可用。

## <a name="self-upgrade-to-gen2"></a>自行升级至 Gen2

可以选择自行升级，方法是在现有 Gen1 数据仓库上执行以下步骤。 如果选择自行升级，则必须在自动升级过程开始之前在所在区域完成它。 这样做可确保避免任何导致冲突的自动升级风险。

进行自行升级时有两种选择。  你可以就地升级当前数据仓库，也可以将 Gen1 数据仓库还原为 Gen2 实例。

- [就地升级](upgrade-to-latest-generation.md) - 此选项会将现有的 Gen1 数据仓库升级到 Gen2。 重启数据仓库时，升级过程会导致连接性短暂下降（大约 5 分钟）。  重启数据仓库后，它将完全可用。 如果在升级过程中遇到问题，请打开[支持请求](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket)并引用 "Gen2 upgrade" 作为可能的原因。
- [从还原点升级](sql-data-warehouse-restore.md) - 在当前 Gen1 数据仓库中创建用户定义的还原点，然后直接还原到 Gen2 实例。 现有的 Gen1 数据仓库将保持现状。 恢复完成后，Gen2 数据仓库将完全可用。  在已还原的 Gen2 实例上运行所有测试和验证过程后，可以删除原始 Gen1 实例。

   - 步骤1：在 Azure 门户中，[创建用户定义的还原点](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal)。
   - 步骤2：从用户定义的还原点还原时，请将 "性能级别" 设置为首选的 Gen2 层。

升级过程继续在后台升级数据文件时，可能会经历一段时间的性能下降。 性能下降的总时间将根据数据文件的大小而有所不同。

要加快后台数据迁移进程，可以通过以更大的 SLO 和资源类对要查询的所有主要列存储表运行 [Alter Index rebuild](sql-data-warehouse-tables-index.md) 来立即强制数据移动。

> [!NOTE]
> Alter Index rebuild 是一项脱机操作，在重新生成完成之前，这些表将不可用。

如果有任何关于数据仓库的问题，请创建[支持请求](sql-data-warehouse-get-started-create-support-ticket.md)并引用“Gen2 升级”作为可能的原因。

有关详细信息，请参阅[升级到 Gen2](upgrade-to-latest-generation.md)。

## <a name="migration-frequently-asked-questions"></a>迁移常见问题

**问： Gen2 成本是否与 Gen1 相同？**

- 答：是的。

**问：升级如何影响我的自动化脚本？**

- 答：引用服务级别目标的任何自动化脚本都应更改为对应于 Gen2 等效项。  详见[此处](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal)。

**问：自升级通常需要多长时间？**

- 答：你可以就地升级或从还原点升级。  
   - 就地升级将导致数据仓库暂时暂停和继续。  数据仓库联机时，后台进程将继续。  
   - 如果要通过还原点进行升级，则需要更长时间，因为升级将完成整个还原过程。

**问：自动升级需要多长时间？**

- 答：升级的实际停机时间只是暂停和恢复服务所需的时间，这介于5到10分钟之间。 在短暂的停机时间之后，后台进程将运行存储迁移。 后台进程的时间长度取决于数据仓库的大小。

**问：何时会进行此自动升级？**

- 答：在维护计划中。 利用选择的维护计划可以最大限度地减少对业务的干扰。

**问：如果我的后台升级过程看似停滞，我该怎么办？**

 - 答：启动对列存储表的重新索引。 请注意，在此操作期间，为表重新编制索引将处于脱机状态。

**问：如果 Gen2 的服务级别目标没有 Gen1，该怎么办？**
- 答：如果你在 Gen1 上运行 DW600 或 DW1200，则建议你分别使用 DW500c 或 DW1000c，因为 Gen2 提供比 Gen1 更多的内存、资源和更高的性能。

**问：是否可以禁用异地备份？**
- 答：没有。 异地备份是一项企业功能，可在区域不可用时保留数据仓库的可用性。 若有其他疑虑，请创建[支持请求](sql-data-warehouse-get-started-create-support-ticket.md)。

**问： Gen1 与 Gen2 之间的 T-sql 语法是否有差异？**

- 答： T-sql 语言语法中没有从 Gen1 到 Gen2 的更改。

**问： Gen2 是否支持维护时段？**

- 答：是的。

**问：我是否能够在区域升级后创建新的 Gen1 实例？**

- 答：没有。 区域升级后，将禁用新 Gen1 实例的创建。

## <a name="next-steps"></a>后续步骤

- [升级步骤](upgrade-to-latest-generation.md)
- [维护时段](maintenance-scheduling.md)
- [资源运行状况监视器](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [开始迁移前查看](upgrade-to-latest-generation.md#before-you-begin)
- [就地升级和从还原点升级](upgrade-to-latest-generation.md)
- [创建用户定义的还原点](sql-data-warehouse-restore-points.md)
- [了解如何还原到 Gen2](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal)
- [创建 SQL 数据仓库支持请求](https://go.microsoft.com/fwlink/?linkid=857950)
