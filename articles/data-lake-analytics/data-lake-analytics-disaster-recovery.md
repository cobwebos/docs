---
title: Azure 数据湖分析的灾难恢复指南
description: 了解如何规划 Azure 数据湖分析帐户的灾难恢复。
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889764"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure 数据湖分析的灾难恢复指南

Azure Data Lake Analytics 是一项按需分析作业服务，用于简化大数据。 无需部署、配置和调整硬件，只需编写查询即可转换数据并提取有价值的见解。 通过将表盘设置为所需值，该分析服务就可以立即处理任何规模的作业。 只需为运行作业付费，让服务变得更为经济高效。 本文提供有关如何保护作业免受罕见区域范围中断或意外删除的指导。

## <a name="disaster-recovery-guidance"></a>灾难恢复指南

使用 Azure 数据湖分析时，准备自己的灾难恢复计划至关重要。 本文帮助您构建灾难恢复计划。 还有其他资源可以帮助您创建自己的计划：
- [Azure 应用程序的故障和灾难恢复](/azure/architecture/reliability/disaster-recovery)
- [Azure 弹性技术指南](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>最佳实践和方案指南

您可以在读取和写入 U-SQL 表以及非结构化数据的区域中的 ADLA 帐户中运行定期 U-SQL 作业。  通过执行以下步骤为灾难做好准备：

1. 在中断期间将使用的辅助区域中创建 ADLA 和 ADLS 帐户。

   > [!NOTE]
   > 由于帐户名称全局是唯一的，请使用一致的命名方案，指示哪个帐户是次要的。

2. 对于非结构化数据，请参阅[Azure 数据湖存储第 1 代中的数据的灾难恢复指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. 对于存储在 ADLA 表和数据库中的结构化数据，创建元数据项目的副本，如数据库、表、表值函数和程序集。 当生产中发生更改时，您需要定期重新同步这些工件。 例如，新插入的数据必须通过复制数据并插入到辅助表中复制到辅助区域。

   > [!NOTE]
   > 这些对象名称的范围限定为辅助帐户，并且不是全局唯一的，因此它们可以具有与主生产帐户中相同的名称。

在中断期间，您需要更新脚本，以便输入路径指向辅助终结点。 然后，用户将其作业提交到辅助区域中的 ADLA 帐户。 然后，作业的输出将写入辅助区域中的 ADLA 和 ADLS 帐户。

## <a name="next-steps"></a>后续步骤

[Azure Data Lake Storage Gen1 数据灾难恢复指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
