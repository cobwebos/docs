---
title: 规划和管理 Azure Blob 存储的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure Blob 存储的成本。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 90aa2b9504008783649662019179a5998d534746
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543089"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>规划和管理 Azure Blob 存储的成本

本文可帮助你规划和管理 Azure Blob 存储的成本。 首先，使用 Azure 定价计算器估算成本。 创建存储帐户后，请优化帐户，以便只为所需的内容付费。 使用成本管理功能来设置预算和监控成本。 你还可以查看预测成本，并监视支出趋势以确定你可能想要采取的措施。

请记住，Azure 存储的费用只是 Azure 帐单中的每月成本。 尽管本文介绍了如何评估和管理 Azure 存储的成本，但你需要为 Azure 订阅使用的所有 Azure 服务和资源（包括第三方服务）付费。 熟悉管理 Azure 存储的成本后，可以应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

## <a name="estimate-costs"></a>估算成本

使用[azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)来估算成本，并开始将数据传输到 Azure 存储帐户。

1. 在[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)页面上，选择 "**存储帐户**" 磁贴。

2. 向下滚动页面并找到估计的 "**存储帐户**" 部分。

3. 从下拉列表中选择 "选项"。 

   当您修改这些下拉列表的值时，成本估算会发生变化。 该估计值显示在上角和估计的底部。 
    
   ![通过成本分析窗格监视成本](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   更改 "**类型**" 下拉列表的值时，此工作表中显示的其他选项也会随之更改。 使用 "**详细信息**" 部分中的链接可以详细了解每个选项的含义，以及这些选项如何影响与存储相关的操作的价格。 

4. 修改剩余的选项，查看其对估计值的影响。

## <a name="optimize-costs"></a>优化成本

请考虑使用这些选项来降低成本。 

- 保留存储容量

- 将数据组织到访问层

- 在访问层之间自动移动数据

本部分更详细地介绍了每个选项。 

#### <a name="reserve-storage-capacity"></a>保留存储容量

你可以通过 Azure 存储预留容量节省 blob 数据的存储成本。 当你在一年或三年内提交到保留时，Azure 存储空间保留容量提供针对块 blob 和标准存储帐户中的 Azure Data Lake Storage Gen2 数据的容量折扣。 预订为保留期提供固定量的存储容量。 Azure 存储预留容量可显著降低块 blob 和 Azure Data Lake Storage Gen2 数据的容量成本。 

若要了解详细信息，请参阅[采用预留容量优化 Blob 存储的成本](https://docs.microsoft.com/azure/storage/blobs/storage-blob-reserved-capacity)。

#### <a name="organize-data-into-access-tiers"></a>将数据组织到访问层

可以通过将 blob 数据置于最经济高效的访问层中来降低成本。 从三个层中进行选择，这些层旨在优化数据使用的成本。 例如，"*热*" 层的存储成本较高，但访问成本更低。 因此，如果你计划经常访问数据，则热层可能是最经济高效的选择。 如果你计划更少地访问数据，*冷*层或*存档*层可能会发挥最大的作用，因为它会提高访问数据的成本，同时降低存储数据的成本。    

若要了解详细信息，请参阅[Azure Blob 存储：热、冷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)。

#### <a name="automatically-move-data-between-access-tiers"></a>在访问层之间自动移动数据

使用生命周期管理策略在各层之间定期移动数据以节省资金。 这些策略可以使用您指定的规则将数据移到。 例如，如果未在90天内修改 blob，则可以创建一个将 blob 移到存档层的规则。 通过创建调整你的数据访问层的策略，你可以根据需要设计开销最少的存储选项。

若要了解详细信息，请参阅[管理 Azure Blob 存储生命周期](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

## <a name="create-budgets"></a>创建预算

可以创建[预算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)来管理成本，并创建警报以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 但是，它们可能会受到有限的功能来管理单个 Azure 服务成本（如 Azure 存储成本），因为它们旨在跟踪更高级别的成本。

## <a name="monitor-costs"></a>监视成本

在 Azure 存储中使用 Azure 资源时，会产生成本。 资源使用情况单位成本因时间间隔（秒、分钟、小时和天）或单位使用量（字节、mb 等）而异。一旦开始使用 Azure 存储，就会产生成本。 您可以在 "[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)" 窗格的 "Azure 门户" 中查看成本。

使用成本分析时，可以在关系图和表中查看不同时间间隔的 Azure 存储成本。 有些示例是按天、当前和上个月和年。 还可以对比预算和预测的成本来查看成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势并了解超支可能发生的位置。 如果您已经创建了预算，还可以轻松地查看他们超出的位置。

>[!NOTE]
> 成本分析支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../../cost-management-billing/costs/assign-access-acm-data.md)。

若要查看成本分析中的 Azure 存储成本：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 打开 "**成本管理 + 计费**" 窗口，从菜单中选择 "**成本管理**"，然后选择 "**成本分析**"。 然后，可以从 "**作用域**" 下拉列表中更改特定订阅的作用域。

   ![通过成本分析窗格监视成本](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. 若要仅查看 Azure 存储的成本，请选择 "**添加筛选器**"，然后选择 "**服务名称**"。 然后从列表中选择 "**存储**"。 

   以下示例显示了 Azure 存储空间的成本：

   ![通过成本分析窗格监视存储成本](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

在前面的示例中，您将看到该服务的当前成本。 Azure 区域（位置）和资源组的成本也会显示。 也可以添加其他筛选器（例如：筛选器查看特定存储帐户的成本）。

## <a name="next-steps"></a>后续步骤

详细了解如何通过[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)来管理成本。

请参阅以下文章，了解有关如何使用 Azure 存储的详细信息：

- [Azure 存储概述定价](https://azure.microsoft.com/pricing/details/storage/)
- [借助预留容量优化 Blob 存储的成本](../blobs/storage-blob-reserved-capacity.md)
