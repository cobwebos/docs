---
title: Azure Data Lake Analytics 的灾难恢复指南
description: 了解如何规划 Azure Data Lake Analytics 帐户的灾难恢复。
services: data-lake-analytics
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ab39ca8e71376fed681c049d338096ff992fed99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132562"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics 的灾难恢复指南

Azure Data Lake Analytics 是一项按需分析作业服务，用于简化大数据。 无需部署、配置和调整硬件，只需编写查询即可转换数据并提取有价值的见解。 通过将表盘设置为所需值，该分析服务就可以立即处理任何规模的作业。 只需为运行作业付费，让服务变得更为经济高效。 本文提供了有关如何在很少发生区域范围的服务中断或意外删除的情况下保护作业的指导。

## <a name="disaster-recovery-guidance"></a>灾难恢复指南

使用 Azure Data Lake Analytics 时，必须准备好自己的灾难恢复计划。 本文介绍如何构建灾难恢复计划。 还有其他资源可帮助你创建自己的计划：
- [Azure 应用程序的故障和灾难恢复](/azure/architecture/reliability/disaster-recovery)
- [Azure 复原技术指南](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>最佳做法和方案指南

可以在区域中的 ADLA 帐户中运行重复的 U SQL 作业，该区域可读取和写入 U SQL 表以及非结构化数据。  通过执行以下步骤来准备灾难：

1. 在将在中断期间使用的次要区域中创建 ADLA 和 ADLS 帐户。

   > [!NOTE]
   > 由于帐户名称是全局唯一的，因此请使用一致的命名方案来指示哪个帐户是辅助帐户。

2. 对于非结构化数据，请参考 [灾难恢复指南以了解 Azure Data Lake Storage Gen1 中的数据](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. 对于存储在 ADLA 表和数据库中的结构化数据，创建元数据项目的副本，如数据库、表、表值函数和程序集。 在生产中发生更改时，需要定期重新同步这些项目。 例如，必须通过复制数据并将其插入到辅助表中，将新插入的数据复制到次要区域。

   > [!NOTE]
   > 这些对象的名称的作用域为辅助帐户，且不是全局唯一的，因此它们的名称可以与主生产帐户中的名称相同。

在中断期间，需要更新脚本，使输入路径指向辅助终结点。 然后，用户将其作业提交到次要区域中的 ADLA 帐户。 然后，该作业的输出将写入到次要区域中的 ADLA 和 ADLS 帐户。

## <a name="next-steps"></a>后续步骤

[Azure Data Lake Storage Gen1 数据灾难恢复指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
