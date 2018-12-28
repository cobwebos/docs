---
title: Azure SQL 数据仓库 Gen2 支持较低的计算层 | Microsoft Docs
description: Azure SQL 数据仓库 Gen2 现支持较低的计算层
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: d3c42c13e58156013167b8570a8845ce9de08b3b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884387"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>Azure SQL 数据仓库 Gen2 支持较低的计算层

Microsoft 通过为运行速度极快的 Azure SQL 数据仓库 Gen2 添加更低的计算层，帮助降低运行能够处理高要求查询的数据仓库的条目分类成本。 客户可以体验 Azure SQL 数据仓库的领先性能、灵活性和安全性功能，从 100 cDWU（数据仓库单元）开始，并在几分钟内扩展到3 0,000 cDWU。 通过较低的计算层，客户可以从 Gen2 性能和灵活性中受益。 

通过降低下一代数据仓库的入口点，Microsoft 为价值驱动型客户打开了大门，他们无需猜测哪种试用环境最适合，即可评估安全、高性能数据仓库的所有优势。  客户将能够从目前的 500 cDWU 入口点开始低至 100 cDWU。  SQL 数据仓库 Gen2 继续支持暂停和恢复操作，并且不仅止于计算的灵活性。  Gen2 还支持无限的列存储容量，并且每个查询的内存增加了 2.5 倍，并发查询多达 128 个，以及自适应缓存功能带来了以下体验：与同一价格的 Gen1 上的相同数据仓库单元相比，平均性能提高了 5 倍。  异地冗余备份是 Gen2 的标准配置，具有内置的保证数据保护。 Azure SQL 数据仓库 Gen2 随时可以缩放。

目前，门户不支持部署或扩展到较低层级的 Gen2 实例。 我们正在努力启用此功能，如果想要充分利用这一新层级，请在此期间[提交工单](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>开始使用 Azure SQL 数据仓库 Gen2 

客户可以选择部署新的 Gen2 实例或升级现有的 Gen1 数据仓库实例，体验下一代数据仓库的灵活性和性能。 

试用 [Azure SQL 数据仓库计算优化的 Gen2 层级。](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)
[将 Azure SQL 仓库计算优化的 Gen1 升级到 Gen2](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation) 在 [Microsoft Mechanics 视频](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)中观看正在活动的 Azure SQL 数据仓库 Gen2。


## <a name="supported-regions-for-lower-compute-tiers"></a>较低计算层的受支持区域

- 美国东部 1 
- 美国东部 2
- 西欧
- 北欧
- 美国西部 2
- 东南亚
- 美国中南部
- 美国中部 
- 东亚
- 日本东部
- 印度中部
- 澳大利亚东部
- 加拿大中部
- 日本西部 
- 加拿大中部

## <a name="next-steps"></a>后续步骤

- 通过升级 SQL 数据仓库了解有关优化性能的[详细信息](upgrade-to-latest-generation.md)。 
