---
title: 计划和管理 Azure 宇宙数据库的成本
description: 了解如何在 Azure 门户中使用成本分析来规划和管理 Azure Cosmos DB 的成本。
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152578"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>计划和管理 Azure 宇宙数据库的成本

本文介绍如何规划和管理 Azure Cosmos DB 的成本。 首先，在添加任何资源之前，使用 Azure Cosmos DB 容量计算器帮助规划成本。 接下来，在添加 Azure 资源时，可以查看估计成本。 开始使用 Azure Cosmos DB 资源后，请使用成本管理功能来设置预算并监视成本。 您还可以查看预测的成本并确定支出趋势，以确定您可能要采取行动的领域。

请注意，Azure Cosmos DB 的成本只是 Azure 帐单中每月成本的一部分。 如果使用其他 Azure 服务，则对 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）进行计费。 本文介绍如何规划和管理 Azure Cosmos DB 的成本。 熟悉 Azure Cosmos DB 的管理成本后，可以应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

## <a name="prerequisites"></a>先决条件

成本分析支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="review-estimated-costs-with-capacity-calculator"></a>使用容量计算器查看估计成本

在 Azure Cosmos 帐户中创建资源之前，请使用[Azure 科斯数据库容量计算器](https://cosmos.azure.com/capacitycalculator/)估计成本。 容量计算器用于估算所需的吞吐量和工作负载成本。 为工作负荷配置 Azure Cosmos 数据库和容器具有适当数量的预配吞吐量或[请求单位 （RU/s）](request-units.md)对于优化成本和性能至关重要。 您必须输入详细信息，如 API 类型、区域数、项目大小、每秒读/写请求、存储的总数据以获取成本估算。 要了解有关容量计算器的更多信息，请参阅[估计](estimate-ru-with-capacity-planner.md)文章。

以下屏幕截图使用容量计算器显示吞吐量和成本估算：

![Azure 宇宙数据库容量计算器中的成本估计](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>从 Azure 门户查看估计成本

从 Azure 门户创建 Azure Cosmos DB 资源时，可以看到估计成本。 使用以下步骤查看成本估算：

1. 登录到 Azure 门户并导航到 Azure Cosmos 帐户。
1. 转到**数据资源管理器**。
1. 创建新容器（如图形容器）。
1. 输入工作负载所需的吞吐量，例如 400 RU/s。 输入吞吐量值后，您可以看到定价估算，如以下屏幕截图所示：

   ![Azure 门户中的成本估计](./media/plan-manage-costs/cost-estimate-portal.png)

如果 Azure 订阅具有支出限制，Azure 会阻止您花费超过信用额度。 创建和使用 Azure 资源时，将使用配额。 达到信用额度时，您部署的资源将禁用该计费周期的其余部分。 您无法更改信用额度，但可以将其删除。 有关支出限制的详细信息，请参阅[Azure 支出限制](../billing/billing-spending-limit.md)。

## <a name="use-budgets-and-cost-alerts"></a>使用预算和成本警报

可以创建[预算](../cost-management/tutorial-acm-create-budgets.md)来管理成本，并创建警报以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 为 Azure 订阅和资源组创建预算和警报，因此它们作为总体成本监视策略的一部分非常有用。 但是，它们可能具有管理单个 Azure 服务成本（如 Azure Cosmos DB 成本）的有限功能，因为它们旨在跟踪更高级别的成本。

## <a name="monitor-costs"></a>监控成本

当您使用与 Azure Cosmos DB 一起使用资源时，会产生成本。 资源使用单位成本因时间间隔（秒、分钟、小时和天）或请求单位使用情况而异。 Azure Cosmos DB 的使用一开始，就会产生成本，您可以在 Azure 门户中[的成本分析](../cost-management/quick-acm-cost-analysis.md)窗格中看到它们。

使用成本分析时，可以在不同时间间隔的图形和表中查看 Azure Cosmos DB 成本。 一些示例按天、当前、上月和年份进行。 您还可以根据预算和预测成本查看成本。 随时间转换到较长的视图可以帮助您确定支出趋势，并了解支出可能发生在哪里。 如果您已创建预算，您还可以轻松查看超出预算的超出位置。要查看成本分析中的 Azure 宇宙数据库成本：

1. 登录到[Azure 门户](https://portal.azure.com)。

1. 打开**成本管理 + 计费**窗口，从菜单中选择**成本管理**，然后选择**成本分析**。 然后，可以从 **"范围"** 下拉列表中更改特定订阅的范围。

1. 默认情况下，所有服务的成本都显示在第一个圆环图中。 选择图表中标记为"Azure 宇宙数据库"的区域。

1. 要缩小单个服务（如 Azure Cosmos DB）的成本，请选择 **"添加筛选器**"，然后选择 **"服务名称**"。 然后，从列表中选择**Azure 宇宙 DB。** 下面是一个示例，显示仅 Azure 宇宙 DB 的成本：
 
   ![使用成本分析窗格监控成本](./media/plan-manage-costs/cost-analysis-pane.png)

在前面的示例中，您可以看到 Azure Cosmos DB 在 2 月当月的当前成本。图表还包含按位置和资源组显示的 Azure Cosmos DB 成本。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关定价在 Azure Cosmos DB 中的工作原理：

* [Azure Cosmos DB 中的定价模型](how-pricing-works.md)
* [在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)
* [优化 Azure Cosmos DB 中的查询成本](optimize-cost-queries.md)
* [优化 Azure Cosmos DB 中的存储成本](optimize-cost-storage.md)