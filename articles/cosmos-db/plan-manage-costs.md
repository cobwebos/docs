---
title: 规划和管理 Azure Cosmos DB 的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure Cosmos DB 的成本。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 96cb22a0153b3506835df93b50785ad7870929ce
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135754"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>规划和管理 Azure Cosmos DB 的成本

本文介绍如何计划和管理 Azure Cosmos DB 的成本。 首先，使用 Azure Cosmos DB 容量计算器在添加任何资源之前，帮助规划成本。 接下来，当你添加 Azure 资源时，你可以查看预估成本。 开始使用 Azure Cosmos DB 资源后，使用成本管理功能来设置预算和监视成本。 你还可以查看预测的成本并确定支出趋势，以确定你可能想要采取的措施。

了解 Azure Cosmos DB 的成本仅是 Azure 帐单中每月费用的一部分。 如果你正在使用其他 Azure 服务，则需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。 本文介绍如何为 Azure Cosmos DB 规划和管理成本。 熟悉管理 Azure Cosmos DB 的成本后，可以应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

## <a name="prerequisites"></a>先决条件

成本分析支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="review-estimated-costs-with-capacity-calculator"></a>通过容量计算器查看预估成本

在 Azure Cosmos 帐户中创建资源之前，请使用[Azure Cosmos DB 容量计算器](https://cosmos.azure.com/capacitycalculator/)来估算成本。 容量计算器用于估算所需的吞吐量和工作负荷的成本。 为工作负荷配置 Azure Cosmos 数据库和容器，使其具有正确的预配吞吐量或[请求单位（RU/s）](request-units.md)，以优化成本和性能。 需要输入详细信息，例如 API 类型、区域数、项大小、每秒读/写请求数、存储的总数据量以获取成本估算。 若要了解有关容量计算器的详细信息，请参阅[估计](estimate-ru-with-capacity-planner.md)文章。

以下屏幕截图显示了使用容量计算器的吞吐量和成本估计：

![Azure Cosmos DB 容量计算器中的成本估算](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>查看 Azure 门户的预估成本

从 Azure 门户创建 Azure Cosmos DB 资源时，可以看到估计的成本。 使用以下步骤查看成本估算：

1. 登录到 Azure 门户并导航到 Azure Cosmos 帐户。
1. 请参阅**数据资源管理器**。
1. 创建新的容器，如图形容器。
1. 输入工作负载所需的吞吐量，例如 400 RU/秒。 输入吞吐量值后，可以看到价格估算，如以下屏幕截图所示：

   ![Azure 门户中的成本估算](./media/plan-manage-costs/cost-estimate-portal.png)

如果你的 Azure 订阅有支出限制，Azure 将阻止你支出支出。 创建和使用 Azure 资源时，将使用信用额度。 达到信用额度限制时，将在该计费周期的剩余时间内禁用部署的资源。 你无法更改信用额度，但可以将其删除。 有关支出限制的详细信息，请参阅[Azure 支出限制](../billing/billing-spending-limit.md)。

## <a name="use-budgets-and-cost-alerts"></a>使用预算和成本警报

你可以创建[预算](../cost-management/tutorial-acm-create-budgets.md)来管理成本，并创建警报，以自动通知利益干系人支出异常和超支风险的利益干系人。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，因此它们在总体成本监视策略中非常有用。 但是，它们的功能有限，无法管理单个 Azure 服务成本（如 Azure Cosmos DB 成本），因为它们旨在跟踪更高级别的成本。

## <a name="monitor-costs"></a>监视成本

在 Azure Cosmos DB 中使用资源时，会产生成本。 资源使用情况单位成本因时间间隔（秒、分钟、小时和天）或请求单位使用情况而异。 一旦开始使用 Azure Cosmos DB，就会产生成本，你可以在 Azure 门户的 "[成本分析](../cost-management/quick-acm-cost-analysis.md)" 窗格中看到它们。

使用成本分析时，可以在关系图和表中查看不同时间间隔的 Azure Cosmos DB 成本。 一些示例如下：日、当前、上个月和年。 你还可以查看预算和预测成本的成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势并了解超支可能发生的位置。 如果您已经创建了预算，还可以轻松地查看他们超出的位置。查看成本分析中的 Azure Cosmos DB 成本：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 打开 "**成本管理 + 计费**" 窗口，从菜单中选择 "**成本管理**"，然后选择 "**成本分析**"。 然后，可以从 "**作用域**" 下拉列表中更改特定订阅的作用域。

1. 默认情况下，在第一个圆环图中显示所有服务的成本。 选择图表中标记为 "Azure Cosmos DB" 的区域。

1. 若要降低单个服务（如 Azure Cosmos DB）的成本，请选择 "**添加筛选器**"，然后选择 "**服务名称**"。 然后从列表中选择 " **Azure Cosmos DB** "。 下面是一个示例，显示只是 Azure Cosmos DB 的成本：
 
   ![通过成本分析窗格监视成本](./media/plan-manage-costs/cost-analysis-pane.png)

在前面的示例中，你将看到2月 Azure Cosmos DB 的当前成本。此图表还包含按位置和按资源组 Azure Cosmos DB 成本。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，详细了解 Azure Cosmos DB 中定价的工作方式：

* [Azure Cosmos DB 中的定价模型](how-pricing-works.md)
* [在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)
* [优化 Azure Cosmos DB 中的查询成本](optimize-cost-queries.md)
* [优化存储成本 Azure Cosmos DB](optimize-cost-storage.md)