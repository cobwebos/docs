---
title: Azure Data Lake Analytics 的灾难恢复指南
description: 了解如何规划灾难恢复的 Azure Data Lake Analytics 帐户。
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66498884"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics 的灾难恢复指南

Azure Data Lake Analytics 是一项按需分析作业服务，用于简化大数据。 无需部署、配置和调整硬件，只需编写查询即可转换数据并提取有价值的见解。 通过将表盘设置为所需值，该分析服务就可以立即处理任何规模的作业。 只需为运行作业付费，让服务变得更为经济高效。 本文指导如何保护你的作业，防范极其少见的全区域服务中断或意外删除行为。

## <a name="disaster-recovery-guidance"></a>灾难恢复指南

使用 Azure Data Lake Analytics，至关重要，以便准备灾难恢复计划。 本文可帮助指导你构建灾难恢复计划。 没有可帮助您创建您自己的计划的其他资源：
- [Azure 应用程序的故障和灾难恢复](/azure/architecture/reliability/disaster-recovery)
- [Azure 复原技术指南](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>最佳实践和方案指南

ADLA 帐户中读取和写入 U-SQL 表，以及非结构化的数据的区域中，可以运行定期 U-SQL 作业。  通过执行这些步骤来准备灾难：

1. 在中断期间将使用在次要区域中创建 ADLA 和 ADLS 帐户。

   > [!NOTE]
   > 由于帐户名称是全局唯一的使用一致的命名方案，该值指示哪个帐户是辅助。

2. 对于非结构化数据，引用[Azure 数据湖存储 Gen1 中的数据的灾难恢复指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. 对于 ADLA 表和数据库中存储的结构化数据，创建的元数据项目，例如数据库、 表、 表值函数和程序集的副本。 您需要在生产环境中发生更改时定期重新同步这些项目。 例如，新插入的数据必须通过将数据复制并将插入到辅助表，会将它们复制到辅助区域。

   > [!NOTE]
   > 这些对象名称的作用域为辅助帐户并不是全局唯一的因此它们可以有如下所示的主生产帐户相同的名称。

在中断期间，您需要更新您的脚本，使输入的路径指向的辅助终结点。 然后用户他们将作业提交到次要区域中的 ADLA 帐户。 然后，作业的输出将写入到次要区域中的 ADLA 和 ADLS 帐户中。

## <a name="next-steps"></a>后续步骤

[Azure 数据湖存储 Gen1 中的数据的灾难恢复指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
